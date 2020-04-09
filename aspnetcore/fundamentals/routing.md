---
title: Routing w ASP.NET Rdzeniu
author: rick-anderson
description: Dowiedz się, jak ASP.NET routingu core jest odpowiedzialny za dopasowanie żądań HTTP i wysyłanie do wykonywalnych punktów końcowych.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/1/2020
uid: fundamentals/routing
ms.openlocfilehash: 5742ac6879ce46e01247ddd2f8bfe3e3b8a2a02a
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80751148"
---
# <a name="routing-in-aspnet-core"></a>Routing w ASP.NET Rdzeniu

Przez [Ryan Nowak](https://github.com/rynowak), Kirk [Larkin](https://twitter.com/serpent5)i [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Routing jest odpowiedzialny za dopasowywanie przychodzących żądań HTTP i wysyłanie tych żądań do wykonywalnych punktów końcowych aplikacji. [Punkty końcowe](#endpoint) to jednostki kodu obsługi żądań wykonywalnych aplikacji. Punkty końcowe są definiowane w aplikacji i konfigurowane po uruchomieniu aplikacji. Proces dopasowywania punktu końcowego można wyodrębnić wartości z adresu URL żądania i podać te wartości do przetwarzania żądań. Korzystając z informacji o punktach końcowych z aplikacji, routing jest również w stanie wygenerować adresy URL, które mapują do punktów końcowych.

Aplikacje mogą konfigurować routing za pomocą:

- Kontrolery
- Razor Pages
- SignalR
- Usługi gRPC
- [Oprogramowanie pośredniczące](xref:fundamentals/middleware/index) z włączoną funkcją punktu końcowego, takie jak [Health Checks](xref:host-and-deploy/health-checks).
- Delegaci i lambdas zarejestrowane z routingu.

Ten dokument zawiera szczegóły niskiego poziomu ASP.NET routingu core. Aby uzyskać informacje na temat konfigurowania routingu:

* Dla kontrolerów, <xref:mvc/controllers/routing>patrz .
* Aby zapoznać się z <xref:razor-pages/razor-pages-conventions>konwencjami strony razor, zobacz .

System routingu punktów końcowych opisany w tym dokumencie ma zastosowanie do ASP.NET Core 3.0 i nowszych. Aby uzyskać informacje na temat <xref:Microsoft.AspNetCore.Routing.IRouter>poprzedniego systemu routingu opartego na programie , wybierz ASP.NET wersję Core 2.1 przy użyciu jednego z następujących metod:

* Selektor wersji dla poprzedniej wersji.
* Wybierz [ASP.NET routingu Core 2.1](https://docs.microsoft.com/aspnet/core/fundamentals/routing?view=aspnetcore-2.1).

[Wyświetl lub pobierz przykładowy kod](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples/3.x) ([jak pobrać](xref:index#how-to-download-a-sample))

Próbki pobierania dla tego dokumentu są włączone `Startup` przez określoną klasę. Aby uruchomić określoną próbkę, zmodyfikuj *Program.cs,* aby wywołać żądaną `Startup` klasę.

## <a name="routing-basics"></a>Podstawowe informacje o routingu

Wszystkie szablony ASP.NET Core obejmują routing w wygenerowanym kodzie. Routing jest zarejestrowany w potoku [oprogramowania pośredniczącego](xref:fundamentals/middleware/index) w `Startup.Configure`pliku .

Poniższy kod przedstawia podstawowy przykład routingu:

[!code-csharp[](routing/samples/3.x/RoutingSample/Startup.cs?name=snippet&highlight=8,10)]

Routing wykorzystuje parę oprogramowania pośredniczącego, zarejestrowanego przez <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> i: <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>

* `UseRouting`dodaje dopasowanie trasy do potoku oprogramowania pośredniczącego. To oprogramowanie pośredniczące analizuje zestaw punktów końcowych zdefiniowanych w aplikacji i wybiera [najlepsze dopasowanie](#urlm) na podstawie żądania.
* `UseEndpoints`dodaje wykonanie punktu końcowego do potoku oprogramowania pośredniczącego. Uruchamia delegata skojarzonego z wybranym punktem końcowym.

W poprzednim przykładzie znajduje się pojedyncza *trasa do* punktu końcowego kodu przy użyciu [MapGet](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*) metody:

* Gdy żądanie `GET` HTTP jest wysyłane `/`do głównego adresu URL:
  * Delegat żądania pokazano wykonuje.
  * `Hello World!`jest zapisywany w odpowiedzi HTTP. Domyślnie głównym adresem `/` `https://localhost:5001/`URL jest .
* Jeśli metoda żądania `GET` nie jest lub `/`główny adres URL nie jest , nie trasy mecze i HTTP 404 jest zwracany.

### <a name="endpoint"></a>Endpoint

<a name="endpoint"></a>

Metoda `MapGet` jest używana do definiowania **punktu końcowego**. Punkt końcowy jest coś, co może być:

* Wybrano, dopasowując adres URL i metodę HTTP.
* Wykonywane, uruchamiając pełnomocnika.

Punkty końcowe, które mogą być dopasowane i wykonywane `UseEndpoints`przez aplikację są konfigurowane w programie . Na przykład <xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*> <xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapPost*>, i [podobne metody](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions) połączyć delegatów żądania do systemu routingu.
Dodatkowe metody mogą być używane do łączenia funkcji ASP.NET Core framework z systemem routingu:
- [MapRazorPages dla stron razor](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*)
- [Kontrolery MapControllers dla kontrolerów](xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*)
- [>\<MapHub THub dla SignalR](xref:Microsoft.AspNetCore.SignalR.HubRouteBuilder.MapHub*) 
- [MapGrpcService\<> dla gRPC](xref:grpc/aspnetcore)

W poniższym przykładzie przedstawiono routing z bardziej zaawansowanym szablonem trasy:

[!code-csharp[](routing/samples/3.x/RoutingSample/RouteTemplateStartup.cs?name=snippet)]

Ciąg `/hello/{name:alpha}` jest **szablonem trasy**. Służy do konfigurowania sposobu dopasowania punktu końcowego. W takim przypadku szablon jest zgodny:

* Adres URL, taki jak`/hello/Ryan`
* Dowolna ścieżka `/hello/` adresu URL, po której następuje sekwencja znaków alfabetycznych.  `:alpha`stosuje ograniczenie trasy, które pasuje tylko do znaków alfabetycznych. [Ograniczenia trasy](#route-constraint-reference) są wyjaśnione w dalszej części tego dokumentu.

Drugi segment ścieżki adresu `{name:alpha}`URL, :

* Jest powiązany `name` z parametrem.
* Jest przechwytywany i przechowywany w [httprequest.RouteValues](xref:Microsoft.AspNetCore.Http.HttpRequest.RouteValues*).

System routingu punktów końcowych opisany w tym dokumencie jest nowy od ASP.NET Core 3.0. Jednak wszystkie wersje ASP.NET Core obsługują ten sam zestaw funkcji szablonu trasy i ograniczeń trasy.

W poniższym przykładzie przedstawiono routing z [kontrolą kondycji](xref:host-and-deploy/health-checks) i autoryzacją:

[!code-csharp[](routing/samples/3.x/RoutingSample/AuthorizationStartup.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

W poprzednim przykładzie pokazano, jak:

* Oprogramowanie pośredniczące autoryzacji może być używane z routingiem.
* Punkty końcowe mogą służyć do konfigurowania zachowania autoryzacji.

Wywołanie <xref:Microsoft.AspNetCore.Builder.HealthCheckEndpointRouteBuilderExtensions.MapHealthChecks*> dodaje punkt końcowy sprawdzania kondycji. Tworzenie <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization*> łańcucha do tego wywołania dołącza zasady autoryzacji do punktu końcowego.

<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> Wywołanie <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization*> i dodaje oprogramowanie pośredniczące uwierzytelniania i autoryzacji. Te oprogramowanie pośredniczące są umieszczone między <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> i `UseEndpoints` tak, że mogą:

* Zobacz, który punkt `UseRouting`końcowy został wybrany przez .
* Zastosuj zasady autoryzacji przed <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> wysyłką do punktu końcowego.

<a name="metadata"></a>

### <a name="endpoint-metadata"></a>Metadane punktu końcowego

W poprzednim przykładzie istnieją dwa punkty końcowe, ale tylko punkt końcowy sprawdzania kondycji ma dołączoną zasadę autoryzacji. Jeśli żądanie jest zgodne z `/healthz`punktem końcowym sprawdzania kondycji, wykonywane jest sprawdzanie autoryzacji. To pokazuje, że punkty końcowe mogą mieć dodatkowe dane dołączone do nich. Te dodatkowe dane są nazywane **metadanymi**punktu końcowego:

* Metadane mogą być przetwarzane przez oprogramowanie pośredniczące obsługujące routing.
* Metadane mogą być dowolnego typu .NET.

## <a name="routing-concepts"></a>Pojęcia dotyczące routingu

System routingu opiera się na potoku oprogramowania pośredniczącego, dodając zaawansowana koncepcja **punktu końcowego.** Punkty końcowe reprezentują jednostki funkcji aplikacji, które różnią się od siebie pod względem routingu, autoryzacji i dowolnej liczby ASP.NET systemów Core.

<a name="endpoint"></a>

### <a name="aspnet-core-endpoint-definition"></a>Definicja punktu końcowego ASP.NET Core

Punktem końcowym ASP.NET Core jest:

* Plik wykonywalny: ma <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate>plik .
* Rozszerzalny: Ma kolekcję [metadanych.](xref:Microsoft.AspNetCore.Http.Endpoint.Metadata*)
* Możliwość wyboru: Opcjonalnie ma [informacje o routingu](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern*).
* Wyliczalne: Kolekcja punktów końcowych może być wymieniona <xref:Microsoft.AspNetCore.Routing.EndpointDataSource> przez pobranie z [DI](xref:fundamentals/dependency-injection).

Poniższy kod pokazuje, jak pobrać i sprawdzić punkt końcowy pasujący do bieżącego żądania:

[!code-csharp[](routing/samples/3.x/RoutingSample/EndpointInspectorStartup.cs?name=snippet)]

Punkt końcowy, jeśli jest zaznaczony, `HttpContext`można pobrać z pliku . Jego właściwości mogą być kontrolowane. Obiekty punktu końcowego są niezmienne i nie można ich zmodyfikować po utworzeniu. Najczęstszym typem punktu końcowego jest . <xref:Microsoft.AspNetCore.Routing.RouteEndpoint> `RouteEndpoint`zawiera informacje, które umożliwiają wybór przez system wyznaczania tras.

W poprzednim kodzie [aplikacji. Użyj](xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*) konfiguruje oprogramowanie [pośredniczące](xref:fundamentals/middleware/index)w wierszu .

<a name="mt"></a>

Poniższy kod pokazuje, że `app.Use` w zależności od tego, gdzie jest wywoływana w potoku, może nie być punkt końcowy:

[!code-csharp[](routing/samples/3.x/RoutingSample/MiddlewareFlowStartup.cs?name=snippet)]

W tym poprzednim `Console.WriteLine` przykładzie dodaje instrukcje, które wyświetlają, czy punkt końcowy został wybrany. Dla jasności przykład przypisuje nazwę wyświetlaną do `/` podanego punktu końcowego.

Uruchamianie tego kodu `/` z adresem URL wyświetla:

```txt
1. Endpoint: (null)
2. Endpoint: Hello
3. Endpoint: Hello
```

Uruchamianie tego kodu z dowolnym innym adresem URL wyświetla:

```txt
1. Endpoint: (null)
2. Endpoint: (null)
4. Endpoint: (null)
```

To dane wyjściowe pokazują, że:

* Punkt końcowy jest zawsze `UseRouting` null przed jest wywoływana.
* Jeśli zostanie znalezione dopasowanie, punkt końcowy jest `UseRouting` <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>nie-null między i .
* Oprogramowanie `UseEndpoints` pośredniczące jest **terminalem** po znalezieniu dopasowania. [Oprogramowanie pośredniczące terminalu](#tm) jest zdefiniowane w dalszej części tego dokumentu.
* Oprogramowanie pośredniczące po `UseEndpoints` wykonaniu tylko wtedy, gdy nie znaleziono dopasowania.

Oprogramowanie `UseRouting` pośredniczące używa [SetEndpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.SetEndpoint*) metody, aby dołączyć punkt końcowy do bieżącego kontekstu. Jest możliwe, aby `UseRouting` zastąpić oprogramowanie pośredniczące z logiką niestandardową i nadal uzyskać korzyści z używania punktów końcowych. Punkty końcowe są niskiego poziomu pierwotne, takie jak oprogramowanie pośredniczące i nie są powiązane z implementacji routingu. Większość aplikacji nie musi `UseRouting` zastępować logiką niestandardową.

Oprogramowanie `UseEndpoints` pośredniczące jest przeznaczone do `UseRouting` stosowania w połączeniu z oprogramowaniem pośredniczącym. Podstawowa logika do wykonania punktu końcowego nie jest skomplikowana. Służy <xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*> do pobierania punktu końcowego, a <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate> następnie wywołać jego właściwości.

Poniższy kod pokazuje, jak oprogramowanie pośredniczące może wpływać na routing lub reagować na:

[!code-csharp[](routing/samples/3.x/RoutingSample/IntegratedMiddlewareStartup.cs?name=snippet)]

W poprzednim przykładzie przedstawiono dwa ważne pojęcia:

* Oprogramowanie pośredniczące `UseRouting` można uruchomić przed zmodyfikowaniem danych, które działa na routingu.
    * Zwykle oprogramowanie pośredniczące, które pojawia się przed marszrutą, modyfikuje niektóre właściwości żądania, takie jak <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>, <xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions.UseHttpMethodOverride*>lub <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*>.
* Oprogramowanie pośredniczące `UseRouting` <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> można uruchomić między i do przetwarzania wyników routingu przed wykonaniem punktu końcowego.
    * Oprogramowanie pośredniczące, które działa między `UseRouting` i: `UseEndpoints`
      * Zazwyczaj sprawdza metadane, aby zrozumieć punkty końcowe.
      * Często podejmuje decyzje dotyczące bezpieczeństwa, jak to zrobić przez `UseAuthorization` i `UseCors`.
    * Połączenie oprogramowania pośredniczącego i metadanych umożliwia konfigurowanie zasad dla punktu końcowego.

Poprzedni kod przedstawia przykład niestandardowego oprogramowania pośredniczącego, który obsługuje zasady punktu końcowego. Oprogramowanie pośredniczące zapisuje *dziennik inspekcji* dostępu do poufnych danych do konsoli. Oprogramowanie pośredniczące można *audit* skonfigurować do inspekcji `AuditPolicyAttribute` punktu końcowego z metadanymi. W tym przykładzie pokazano wzorzec *opt-in, w* którym tylko punkty końcowe, które są oznaczone jako poufne są poddane inspekcji. Można zdefiniować tę logikę w odwrotnej kolejności, na przykład inspekcji wszystko, co nie jest oznaczone jako bezpieczne. System metadanych punktu końcowego jest elastyczny. Ta logika może być zaprojektowana w dowolny sposób, który pasuje do przypadku użycia.

Poprzedni przykładowy kod ma na celu zademonstrowanie podstawowych pojęć punktów końcowych. **Próbka nie jest przeznaczona do użytku produkcyjnego.** Bardziej kompletna wersja oprogramowania pośredniczącego *dziennika inspekcji:*

* Zaloguj się do pliku lub bazy danych.
* Podaj szczegóły, takie jak użytkownik, adres IP, nazwa poufnego punktu końcowego i inne.

Metadane `AuditPolicyAttribute` zasad inspekcji jest `Attribute` zdefiniowany jako łatwiejsze do użytku z klasowych struktur, takich jak kontrolery i SignalR. W przypadku *używania trasy do kodu:*

* Metadane są dołączone do interfejsu API konstruktora.
* Struktury oparte na klasach zawierają wszystkie atrybuty odpowiedniej metody i klasy podczas tworzenia punktów końcowych.

Najlepszym rozwiązaniem dla typów metadanych jest zdefiniowanie ich jako interfejsów lub atrybutów. Interfejsy i atrybuty umożliwiają ponowne użycie kodu. System metadanych jest elastyczny i nie nakłada żadnych ograniczeń.

<a name="tm"></a>

### <a name="comparing-a-terminal-middleware-and-routing"></a>Porównywanie oprogramowania pośredniczącego terminala i routingu

Poniższy przykładowy kod kontrastuje przy użyciu oprogramowania pośredniczącego przy użyciu routingu:

[!code-csharp[](routing/samples/3.x/RoutingSample/TerminalMiddlewareStartup.cs?name=snippet)]

Styl oprogramowania pośredniczącego pokazane z `Approach 1:` jest oprogramowanie **pośredniczące terminalu**. Nazywa się oprogramowanie pośredniczące terminalu, ponieważ wykonuje pasujące działanie:

* Pasujące operacji w poprzednim `Path == "/"` przykładzie jest `Path == "/Movie"` dla oprogramowania pośredniczącego i routingu.
* Gdy dopasowanie zakończy się pomyślnie, wykonuje niektóre funkcje i `next` zwraca, zamiast wywoływania oprogramowania pośredniczącego.

Nazywa się oprogramowanie pośredniczące terminalu, ponieważ kończy wyszukiwanie, wykonuje niektóre funkcje, a następnie zwraca.

Porównanie oprogramowania pośredniczącego terminala i routingu:
* Oba podejścia umożliwiają zakończenie potoku przetwarzania:
    * Oprogramowanie pośredniczące kończy potok przez zwrócenie, a nie wywoływanie `next`.
    * Punkty końcowe są zawsze końcowe.
* Oprogramowanie pośredniczące terminalu umożliwia pozycjonowanie oprogramowania pośredniczącego w dowolnym miejscu w potoku:
    * Punkty końcowe są wykonywane <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>w pozycji .
* Oprogramowanie pośredniczące terminalu umożliwia dowolny kod, aby określić, kiedy oprogramowanie pośredniczące pasuje:
    * Niestandardowy kod dopasowania trasy może być pełny i trudny do poprawnego zapisu.
    * Routing zapewnia proste rozwiązania dla typowych aplikacji. Większość aplikacji nie wymaga niestandardowego kodu dopasowania trasy.
* Interfejs punktów końcowych z oprogramowaniem pośredniczącym, takim jak `UseAuthorization` i `UseCors`.
    * Korzystanie z oprogramowania `UseAuthorization` `UseCors` pośredniczącego terminala z systemem autoryzacji lub wymaga ręcznego połączenia z systemem autoryzacji.

[Punkt końcowy](#endpoint) definiuje zarówno:

* Pełnomocnik do przetwarzania żądań.
* Kolekcja dowolnych metadanych. Metadane są używane do implementowania problemów przekrojowych na podstawie zasad i konfiguracji dołączonych do każdego punktu końcowego.

Oprogramowanie pośredniczące terminalu może być skutecznym narzędziem, ale może wymagać:

* Znaczna ilość kodowania i testowania.
* Ręczna integracja z innymi systemami w celu osiągnięcia pożądanego poziomu elastyczności.

Należy rozważyć integrację z routingu przed napisaniem oprogramowania pośredniczącego terminala.

Istniejące oprogramowanie pośredniczące terminalu, które integruje się z [mapą](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) lub <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> zwykle można przekształcić w punkt końcowy obsługujący routing. [MapHealthChecks](https://github.com/aspnet/AspNetCore/blob/master/src/Middleware/HealthChecks/src/Builder/HealthCheckEndpointRouteBuilderExtensions.cs#L16) pokazuje wzorzec router-ware:
* Zapisz metodę rozszerzenia <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder>na .
* Utwórz zagnieżdżony potok oprogramowania pośredniczącego przy użyciu programu <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.CreateApplicationBuilder*>.
* Dołącz oprogramowanie pośredniczące do nowego potoku. W tym <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>przypadku .
* <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.Build*>potoku oprogramowania <xref:Microsoft.AspNetCore.Http.RequestDelegate>pośredniczącego do pliku .
* Wywołanie `Map` i udostępnij nowy potok oprogramowania pośredniczącego.
* Zwraca obiekt konstruktora dostarczony przez `Map` metodę rozszerzenia.

Poniższy kod pokazuje korzystanie z [MapHealthChecks:](xref:host-and-deploy/health-checks)

[!code-csharp[](routing/samples/3.x/RoutingSample/AuthorizationStartup.cs?name=snippet)]

W poprzednim przykładzie pokazano, dlaczego zwracanie obiektu konstruktora jest ważne. Zwracanie obiektu konstruktora umożliwia deweloperowi aplikacji skonfigurowanie zasad, takich jak autoryzacja dla punktu końcowego. W tym przykładzie oprogramowanie pośredniczące kontroli kondycji nie ma bezpośredniej integracji z systemem autoryzacji.

System metadanych został utworzony w odpowiedzi na problemy napotkane przez autorów rozszerzalności przy użyciu oprogramowania pośredniczącego terminala. Jest problematyczne dla każdego oprogramowania pośredniczącego do zaimplementowania własnej integracji z systemem autoryzacji.

<a name="urlm"></a>

### <a name="url-matching"></a>Dopasowywanie adresów URL

* Jest procesem, w którym routing pasuje do żądania przychodzącego do [punktu końcowego](#endpoint).
* Jest oparty na danych w ścieżce adresu URL i nagłówkach.
* Można rozszerzyć, aby uwzględnić wszelkie dane w żądaniu.

Podczas wykonywania oprogramowania pośredniczącego routingu ustawia `Endpoint` wartości i trasy do funkcji [żądania](xref:fundamentals/request-features) na <xref:Microsoft.AspNetCore.Http.HttpContext> z bieżącego żądania:

* Wywołanie [httpContext.GetEndpoint](<xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*>) pobiera punkt końcowy.
* `HttpRequest.RouteValues`pobiera kolekcję wartości trasy.

[Oprogramowanie pośredniczące](xref:fundamentals/middleware/index) uruchomione po oprogramowaniu pośredniczącym routingu może sprawdzić punkt końcowy i podjąć działania. Na przykład oprogramowanie pośredniczące autoryzacji może przesłuchiwać kolekcję metadanych punktu końcowego dla zasad autoryzacji. Po wykonaniu całego oprogramowania pośredniczącego w potoku przetwarzania żądań wywoływany jest delegat wybranego punktu końcowego.

System routingu w routingu punktu końcowego jest odpowiedzialny za wszystkie decyzje dotyczące wysyłki. Ponieważ oprogramowanie pośredniczące stosuje zasady na podstawie wybranego punktu końcowego, ważne jest, aby:

* Każda decyzja, która może mieć wpływ na wysyłkę lub stosowanie zasad zabezpieczeń, jest podejmowana wewnątrz systemu routingu.

> [!WARNING]
> W przypadku zgodności z powrotem, gdy kontroler lub Razor Pages delegata punktu końcowego jest wykonywana, właściwości [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) są ustawione na odpowiednie wartości na podstawie przetwarzania żądań wykonywane do tej pory.
>
> Typ `RouteContext` zostanie oznaczony jako przestarzały w przyszłej wersji:
>
> * `RouteData.Values` Migrowanie `HttpRequest.RouteValues`do pliku .
> * Migruj, `RouteData.DataTokens` aby pobrać [IDataTokensMetadata](xref:Microsoft.AspNetCore.Routing.IDataTokensMetadata) z metadanych punktu końcowego.

Dopasowywanie adresów URL działa w konfigurowalnym zestawie faz. W każdej fazie dane wyjściowe są zestawem dopasowań. Zestaw meczów można zawęzić do następnej fazy. Implementacja routingu nie gwarantuje kolejność przetwarzania dla pasujących punktów końcowych. **Wszystkie** możliwe dopasowania są przetwarzane jednocześnie. Fazy dopasowywania adresów URL występują w następującej kolejności. ASP.NET Core:

1. Przetwarza ścieżkę adresu URL względem zestawu punktów końcowych i ich szablonów tras, zbierając **wszystkie** dopasowania.
1. Przyjmuje poprzednią listę i usuwa dopasowania, które nie powiodą się z zastosowanymi ograniczeniami trasy.
1. Przyjmuje poprzednią listę i usuwa dopasowania, które nie powiedzie się zestaw [matcherpolicy](xref:Microsoft.AspNetCore.Routing.MatcherPolicy) wystąpień.
1. Używa [endpointSelector](xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector) do podjęcia ostatecznej decyzji z poprzedniej listy.

Lista punktów końcowych jest traktowana priorytetowo zgodnie z:

* [RouteEndpoint.Order](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order*)
* [Pierwszeństwo szablonu trasy](#rtp)

Wszystkie pasujące punkty końcowe są przetwarzane <xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector> w każdej fazie, aż do osiągnięcia. Jest `EndpointSelector` to faza końcowa. Wybiera punkt końcowy o najwyższym priorytecie z dopasowań jako najlepsze dopasowanie. Jeśli istnieją inne mecze o tym samym priorytecie co najlepsze dopasowanie, zgłaszany jest niejednoznaczny wyjątek dopasowania.

Pierwszeństwo trasy jest obliczane na podstawie **bardziej szczegółowego** szablonu trasy, który ma wyższy priorytet. Rozważmy na przykład `/hello` szablony i: `/{message}`

* Oba są zgodne `/hello`ze ścieżką adresu URL .
* `/hello`jest bardziej szczegółowa, a zatem wyższy priorytet.

Ogólnie rzecz biorąc pierwszeństwo trasy ma dobrą robotę, wybierając najlepsze dopasowanie dla rodzajów schematów adresów URL używanych w praktyce. Używaj <xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order> tylko wtedy, gdy jest to konieczne, aby uniknąć niejednoznaczności.

Ze względu na rodzaje rozszerzalności zapewniane przez routing, nie jest możliwe dla systemu routingu do obliczania z wyprzedzeniem niejednoznaczne trasy. Rozważmy przykład, taki jak `/{message:alpha}` `/{message:int}`szablony tras i:

* Ograniczenie `alpha` jest zgodne tylko z znakami alfabetycznymi.
* Ograniczenie `int` jest zgodne tylko z liczbami.
* Te szablony mają ten sam priorytet trasy, ale nie ma jednego adresu URL, który są zgodne.
* Jeśli system routingu zgłosił błąd niejednoznaczności podczas uruchamiania, zablokuje ten prawidłowy przypadek użycia.

> [!WARNING]
>
> Kolejność operacji wewnątrz <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> nie wpływa na zachowanie routingu, z jednym wyjątkiem. <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>i <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> automatycznie przypisać wartość zamówienia do swoich punktów końcowych na podstawie kolejności, w jakiej są wywoływane. Symuluje to długotrwałe zachowanie kontrolerów bez systemu routingu, zapewniając takie same gwarancje jak starsze implementacje routingu.
>
> W starszej implementacji routingu jest możliwe do zaimplementowania rozszerzalności routingu, który ma zależność od kolejności, w której trasy są przetwarzane. Routing punktu końcowego w ASP.NET Core 3.0 i nowszych:
> 
> * Nie ma pojęcia tras.
> * Nie zapewnia gwarancji zamawiania. Wszystkie punkty końcowe są przetwarzane jednocześnie.
>
> Jeśli oznacza to, że utkniesz przy użyciu starszego systemu routingu, [otwórz problem z githubem, aby uzyskać pomoc.](https://github.com/dotnet/aspnetcore/issues)

<a name="rtp"></a>

### <a name="route-template-precedence-and-endpoint-selection-order"></a>Pierwszeństwo szablonu marszruty i kolejność wyboru punktu końcowego

[Pierwszeństwo szablonu trasy](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L16) to system, który przypisuje każdemu szablonowi trasy wartość na podstawie jego specyfiki. Pierwszeństwo szablonu trasy:

* Pozwala uniknąć konieczności dostosowywania kolejności punktów końcowych w typowych przypadkach.
* Próbuje dopasować oczekiwania zdrowego rozsądku zachowania routingu.

Rozważmy na przykład `/Products/List` `/Products/{id}`szablony i . Byłoby rozsądne, aby `/Products/List` założyć, że `/Products/{id}` jest lepsze `/Products/List`dopasowanie niż dla ścieżki adresu URL . Działa, ponieważ segment `/List` dosłowny jest uważany za `/{id}`lepszy niż segment parametru .

Szczegóły dotyczące funkcjonowania pierwszeństwa są powiązane ze sposobu definiowania szablonów tras:

* Szablony z większą liczesz segmentami są uważane za bardziej szczegółowe.
* Segment z tekstem dosłownym jest uważany za bardziej specyficzny niż segment parametrów.
* Segment parametrów z ograniczeniem jest uważany za bardziej szczegółowy niż jeden bez.
* Segment złożony jest uważany za specyficzny jako segment parametrów z ograniczeniem.
* Catch wszystkie parametry są najmniej specyficzne.

Zobacz [kod źródłowy w usłudze GitHub,](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L189) aby uzyskać odwołanie do dokładnych wartości.

<a name="lg"></a>

### <a name="url-generation-concepts"></a>Pojęcia generowania adresów URL

Generowanie adresu URL:

* Jest to proces, w którym routing może utworzyć ścieżkę adresu URL na podstawie zestawu wartości trasy.
* Umożliwia logiczne oddzielenie między punktami końcowymi i adresami URL, które do nich uzyskują dostęp.

Routing punktu końcowego obejmuje interfejs <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API. `LinkGenerator`jest usługą singleton dostępną w [DI.](xref:fundamentals/dependency-injection) Interfejs `LinkGenerator` API może służyć poza kontekstem żądania wykonywania. [Mvc.IUrlHelper](xref:Microsoft.AspNetCore.Mvc.IUrlHelper) i scenariusze, <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>na których opierają się , takie jak [Pomocnicy tagów,](xref:mvc/views/tag-helpers/intro)Pomocnicy HTML i [Wyniki akcji,](xref:mvc/controllers/actions)używają `LinkGenerator` interfejsu API wewnętrznie, aby zapewnić możliwości generowania łączy.

Generator łączy jest wspierany przez koncepcję systemów **adresów** i **adresów**. Schemat adresów jest sposobem określania punktów końcowych, które powinny być brane pod uwagę dla generowania łączy. Na przykład scenariusze wartości nazwy trasy i trasy, które wielu użytkowników zna z kontrolerów, a strony Razor są implementowane jako schemat adresów.

Generator łączy może łączyć się z kontrolerami i stronami razor za pomocą następujących metod rozszerzenia:

* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*>
* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetPathByPage*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetUriByPage*>

Przeciążenia tych metod akceptują `HttpContext`argumenty, które obejmują . Metody te są funkcjonalnie równoważne [Url.Action](xref:System.Web.Mvc.UrlHelper.Action*) i [Url.Page](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*), ale oferują dodatkową elastyczność i opcje.

Metody `GetPath*` są najbardziej `Url.Action` podobne `Url.Page`do i , w tym generują identyfikator URI zawierający ścieżkę bezwzględną. Metody `GetUri*` zawsze generują bezwzględny identyfikator URI zawierający schemat i host. Metody, które `HttpContext` akceptują generowanie identyfikatora URI w kontekście żądania wykonywania. Wartości trasy [otoczenia,](#ambient) ścieżka bazowa adresu URL, schemat i host z żądania wykonywania są używane, chyba że zostaną zastąpione.

<xref:Microsoft.AspNetCore.Routing.LinkGenerator>nazywa się z adresem. Generowanie identyfikatora URI odbywa się w dwóch etapach:

1. Adres jest powiązany z listą punktów końcowych, które pasują do adresu.
1. Każdy punkt końcowy <xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern> jest obliczany, dopóki nie zostanie znaleziony wzorzec trasy, który pasuje do podanych wartości. Wynikowe wyjście jest łączone z innymi częściami URI dostarczonymi do generatora łączy i zwróconymi.

Metody zapewniane <xref:Microsoft.AspNetCore.Routing.LinkGenerator> przez obsługę standardowych możliwości generowania łączy dla dowolnego typu adresu. Najwygodniejszym sposobem użycia generatora łączy są metody rozszerzenia, które wykonują operacje dla określonego typu adresu:

| Metoda rozszerzenia | Opis |
| ---------------- | ----------- |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | Generuje identyfikator URI ze ścieżką bezwzględną na podstawie podanych wartości. |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | Generuje bezwzględny identyfikator URI na podstawie podanych wartości.             |

> [!WARNING]
> Należy zwrócić uwagę na następujące <xref:Microsoft.AspNetCore.Routing.LinkGenerator> implikacje metod wywoływania:
>
> * Użyj `GetUri*` metod rozszerzenia z ostrożnością w konfiguracji aplikacji, która nie sprawdza poprawności `Host` nagłówka żądań przychodzących. Jeśli `Host` nagłówek żądań przychodzących nie jest zweryfikowany, niezaufane dane wejściowe żądania mogą być wysyłane z powrotem do klienta w identyfikatorach URI w widoku lub na stronie. Zaleca się, aby wszystkie aplikacje produkcyjne `Host` skonfigurować swój serwer, aby sprawdzić poprawność nagłówka względem znanych prawidłowych wartości.
>
> * Należy <xref:Microsoft.AspNetCore.Routing.LinkGenerator> zachować ostrożność w `Map` oprogramowaniu pośredniczącym w połączeniu z programem lub . `MapWhen` `Map*`zmienia ścieżkę podstawową żądania wykonywania, co wpływa na dane wyjściowe generowania łączy. Wszystkie interfejsy <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API umożliwiają określanie ścieżki podstawowej. Określ pustą ścieżkę `Map*` podstawową, aby cofnąć wpływ na generowanie łączy.

### <a name="middleware-example"></a>Przykład oprogramowania pośredniczącego

W poniższym przykładzie oprogramowanie pośredniczące używa <xref:Microsoft.AspNetCore.Routing.LinkGenerator> interfejsu API do utworzenia łącza do metody akcji, która zawiera listę produktów magazynu. Korzystanie z generatora łączy przez wstrzyknięcie `GenerateLink` go do klasy i wywołanie jest dostępna dla każdej klasy w aplikacji:

[!code-csharp[](routing/samples/3.x/RoutingSample/Middleware/ProductsLinkMiddleware.cs?name=snippet)]

<a name="rtr"></a>

## <a name="route-template-reference"></a>Odwołanie do szablonu trasy

Tokeny `{}` w ramach definiowania parametrów trasy, które są powiązane, jeśli trasa jest dopasowana. W segmencie trasy można zdefiniować więcej niż jeden parametr trasy, ale parametry trasy muszą być oddzielone wartością literału. Na przykład `{controller=Home}{action=Index}` nie jest prawidłową trasą, ponieważ nie `{controller}` ma `{action}`wartości literału między i .  Parametry trasy muszą mieć nazwę i mogą mieć określone dodatkowe atrybuty.

Tekst dosłowny inny niż `{id}`parametry trasy (na `/` przykład) i separator ścieżki muszą być zgodne z tekstem w adresie URL. Dopasowanie tekstu jest niewrażliwe na rozmiary i opiera się na zdekodowanym reprezentacji ścieżki adresów URL. Aby dopasować dosłowny `{` parametr `}`trasy ogranicznik lub , uciec ogranicznika, powtarzając znak. Na `{{` przykład `}}`lub .

Gwiazdka `*` lub podwójna `**`gwiazdka:

* Może służyć jako prefiks do parametru trasy do powiązania z resztą identyfikatora URI.
* Są nazywane **catch-all** parametrów. Na przykład: `blog/{**slug}`
  * Dopasowuje dowolny identyfikator `/blog` URI, który rozpoczyna się od i ma dowolną wartość po nim.
  * Następująca `/blog` wartość jest przypisywana do wartości trasy [ślimaka.](https://developer.mozilla.org/docs/Glossary/Slug)

Catch-all parametry mogą również odpowiadać pusty ciąg.

Parametr catch-all unika odpowiednich znaków, gdy trasa jest używana do generowania adresu URL, w tym znaków separatora `/` ścieżki. Na przykład trasa `foo/{*path}` z `{ path = "my/path" }` wartościami `foo/my%2Fpath`trasy generuje . Zwróć uwagę na ukucie do przodu. Aby znaki separatora ścieżki w `**` obie strony, należy użyć prefiksu parametru trasy. Trasa `foo/{**path}` z `{ path = "my/path" }` generuje `foo/my/path`.

Wzorce adresów URL, które próbują przechwycić nazwę pliku z opcjonalnym rozszerzeniem pliku, mają dodatkowe uwagi. Rozważmy na przykład `files/{filename}.{ext?}`szablon . Gdy wartości `filename` dla `ext` obu i istnieją, obie wartości są wypełniane. Jeśli w adresie URL `filename` istnieje tylko wartość, trasa `.` jest zgodna, ponieważ końcowe jest opcjonalne. Następujące adresy URL są zgodne z tą trasą:

* `/files/myFile.txt`
* `/files/myFile`

Parametry trasy mogą mieć **wartości domyślne** wyznaczone przez określenie wartości domyślnej po`=`nazwie parametru oddzielonej znakiem równości ( ). Na przykład `{controller=Home}` definiuje `Home` jako wartość `controller`domyślną dla . Wartość domyślna jest używana, jeśli w adresie URL parametru nie ma żadnej wartości. Parametry trasy są opcjonalne przez dołączenie`?`znaku zapytania ( ) na końcu nazwy parametru. Na przykład `id?`. Różnica między wartościami opcjonalnymi a domyślnymi parametrami trasy wynosi:

* Parametr trasy z wartością domyślną zawsze generuje wartość.
* Parametr opcjonalny ma wartość tylko wtedy, gdy wartość jest dostarczana przez adres URL żądania.

Parametry trasy mogą mieć ograniczenia, które muszą odpowiadać wartości trasy powiązanej z adresem URL. Dodanie `:` i nazwa ograniczenia po nazwie parametru trasy określa ograniczenie wbudowane dla parametru trasy. Jeśli ograniczenie wymaga argumentów, są one ujęte w nawiasy `(...)` po nazwie ograniczenia. Wiele *ograniczeń wbudowanych* można określić, `:` dołączając inną nazwę i nazwę ograniczenia.

Nazwa ograniczenia i argumenty są <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> przekazywane do usługi, aby utworzyć wystąpienie do użycia w przetwarzaniu adresów <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> URL. Na przykład szablon `blog/{article:minlength(10)}` trasy określa `minlength` ograniczenie z `10`argumentem . Aby uzyskać więcej informacji na temat ograniczeń trasy i listy ograniczeń przewidzianych przez strukturę, zobacz [sekcja Odwołanie do ograniczenia trasy.](#route-constraint-reference)

Parametry trasy mogą również mieć transformatory parametrów. Transformatory parametrów przekształcają wartość parametru podczas generowania łączy i dopasowywania akcji i stron do adresów URL. Podobnie jak wiązania, transformatory parametrów można dodać w `:` linii do parametru trasy, dodając nazwę a i transformatora po nazwie parametru trasy. Na przykład szablon `blog/{article:slugify}` trasy określa `slugify` transformator. Aby uzyskać więcej informacji na temat transformatorów parametrów, zobacz [sekcję Odwołanie do transformatora parametrów.](#parameter-transformer-reference)

W poniższej tabeli przedstawiono przykładowe szablony tras i ich zachowanie:

| Szablon trasy                           | Przykładowy pasujący identyfikator URI    | Identyfikator URI żądania&hellip;                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | Pasuje tylko do `/hello`pojedynczej ścieżki .                                     |
| `{Page=Home}`                            | `/`                     | Dopasowuje `Page` `Home`i ustawia na .                                         |
| `{Page=Home}`                            | `/Contact`              | Dopasowuje `Page` `Contact`i ustawia na .                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | Mapy do `Products` kontrolera i `List` akcji.                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | Mapy do `Products` kontrolera `Details` `id` i akcji z zestawem 123. |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | Mapy do `Home` kontrolera i `Index` metody. Parametr `id` jest ignorowany.        |
| `{controller=Home}/{action=Index}/{id?}` | `/Products`         | Mapy do `Products` kontrolera i `Index` metody. Parametr `id` jest ignorowany.        |

Korzystanie z szablonu jest zazwyczaj najprostszym podejściem do routingu. Ograniczenia i wartości domyślne można również określić poza szablonem trasy.

### <a name="complex-segments"></a>Segmenty złożone

Złożone segmenty są przetwarzane przez dopasowanie do literału ograniczników od prawej do lewej w sposób [niechciwy.](#greedy) Na przykład `[Route("/a{b}c{d}")]` jest segmentem złożonym.
Złożone segmenty działają w określony sposób, który musi być rozumiany jako z powodzeniem z nich korzystać. W przykładzie w tej sekcji pokazano, dlaczego złożone segmenty działają tylko dobrze, gdy tekst ogranicznika nie pojawia się wewnątrz wartości parametrów. Przy użyciu [wyrażenia regularnego,](/dotnet/standard/base-types/regular-expressions) a następnie ręcznie wyodrębnianie wartości jest potrzebne w bardziej złożonych przypadkach.

Jest to podsumowanie kroków, które routing `/a{b}c{d}` wykonuje z `/abcd`szablonem i ścieżką adresu URL . Służy `|` do wizualizacji, jak działa algorytm:

* Pierwszym dosłownym, od `c`prawej do lewej, jest . Tak `/abcd` jest wyszukiwany od `/ab|c|d`prawej i znajduje .
* Wszystko po prawej`d`stronie ( ) jest `{d}`teraz dopasowane do parametru trasy .
* Następny dosłowny, od `a`prawej do lewej, to . Tak `/ab|c|d` jest przeszukiwany, począwszy `a` od `/|a|b|c|d`miejsca, w którym po zostało przerwane, a następnie znajduje się .
* Wartość po prawej`b`stronie ( ) jest teraz `{b}`dopasowywać do parametru trasy .
* Nie ma pozostałego tekstu ani pozostałego szablonu trasy, więc jest to dopasowanie.

Oto przykład negatywnego przypadku przy użyciu `/a{b}c{d}` tego samego `/aabcd`szablonu i ścieżki adresu URL . Służy `|` do wizualizacji, jak działa algorytm. Ten przypadek nie jest dopasowaniem, co jest wyjaśnione przez ten sam algorytm:
* Pierwszym dosłownym, od `c`prawej do lewej, jest . Tak `/aabcd` jest wyszukiwany od `/aab|c|d`prawej i znajduje .
* Wszystko po prawej`d`stronie ( ) jest `{d}`teraz dopasowane do parametru trasy .
* Następny dosłowny, od `a`prawej do lewej, to . Tak `/aab|c|d` jest przeszukiwany, począwszy `a` od `/a|a|b|c|d`miejsca, w którym po zostało przerwane, a następnie znajduje się .
* Wartość po prawej`b`stronie ( ) jest teraz `{b}`dopasowywać do parametru trasy .
* W tym momencie `a`jest pozostały tekst , ale algorytm zabrakło szablonu trasy do analizy, więc nie jest to dopasowanie.

Ponieważ algorytm dopasowania jest [niechciwy:](#greedy)

* Odpowiada najmniejszej możliwej ilości tekstu w każdym kroku.
* W każdym przypadku, gdy wartość ogranicznika pojawia się wewnątrz wartości parametrów powoduje, że nie pasujące.

Wyrażenia regularne zapewniają znacznie większą kontrolę nad ich zachowaniem dopasowania.

<a name="greedy"></a>

Chciwy dopasowywanie, znany również jako [leniwy dopasowania,](https://wikipedia.org/wiki/Regular_expression#Lazy_matching)pasuje do największego możliwego ciągu. Non-chciwy pasuje do najmniejszego możliwego ciągu.

## <a name="route-constraint-reference"></a>Odwołanie do ograniczenia trasy

Ograniczenia trasy są wykonywane, gdy dopasowanie wystąpiło z przychodzącym adresem URL, a ścieżka adresu URL jest tokenizowana na wartości trasy. Ograniczenia trasy zazwyczaj sprawdzają wartość trasy skojarzoną za pośrednictwem szablonu trasy i podejmują prawdziwą lub fałszywą decyzję o tym, czy wartość jest dopuszczalna. Niektóre ograniczenia trasy używają danych poza wartością trasy, aby rozważyć, czy żądanie może być kierowane. Na przykład <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> można zaakceptować lub odrzucić żądanie na podstawie jego zlecenia HTTP. Ograniczenia są używane w żądaniach routingu i generowaniu łączy.

> [!WARNING]
> Nie używaj ograniczeń do sprawdzania poprawności danych wejściowych. Jeśli ograniczenia są używane do sprawdzania poprawności `404` danych wejściowych, nieprawidłowe wyniki wejściowe w odpowiedzi nie znaleziono. Nieprawidłowe dane `400` wejściowe powinny spowodować nieprawidłowe żądanie z odpowiednim komunikatem o błędzie. Ograniczenia trasy są używane do rozróżniania podobnych tras, a nie do sprawdzania poprawności danych wejściowych dla określonej trasy.

W poniższej tabeli przedstawiono przykładowe ograniczenia trasy i ich oczekiwane zachowanie:

| ograniczenie | Przykład | Przykładowe dopasowania | Uwagi |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | `123456789`, `-123456789` | Dopasowuje dowolną całkowitą |
| `bool` | `{active:bool}` | `true`, `FALSE` | Pasuje `true` `false`lub . Bez uwzględniania wielkości liter |
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | Pasuje do `DateTime` prawidłowej wartości w kulturze niezmiennej. Zobacz poprzednie ostrzeżenie. |
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Pasuje do `decimal` prawidłowej wartości w kulturze niezmiennej. Zobacz poprzednie ostrzeżenie.|
| `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | Pasuje do `double` prawidłowej wartości w kulturze niezmiennej. Zobacz poprzednie ostrzeżenie.|
| `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | Pasuje do `float` prawidłowej wartości w kulturze niezmiennej. Zobacz poprzednie ostrzeżenie.|
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638` | Dopasowuje `Guid` prawidłową wartość |
| `long` | `{ticks:long}` | `123456789`, `-123456789` | Dopasowuje `long` prawidłową wartość |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | Ciąg musi zawierać co najmniej 4 znaki |
| `maxlength(value)` | `{filename:maxlength(8)}` | `MyFile` | Ciąg nie może zawierać więcej niż 8 znaków |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | Ciąg musi mieć dokładnie 12 znaków |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | Ciąg musi mieć co najmniej 8 znaków i nie więcej niż 16 znaków |
| `min(value)` | `{age:min(18)}` | `19` | Wartość całkowita musi wynosić co najmniej 18 |
| `max(value)` | `{age:max(120)}` | `91` | Wartość całkowita nie może być większa niż 120 |
| `range(min,max)` | `{age:range(18,120)}` | `91` | Wartość całkowita musi wynosić co najmniej 18, ale nie więcej niż 120 |
| `alpha` | `{name:alpha}` | `Rick` | Ciąg musi składać się z jednego `a` - `z` lub więcej znaków alfabetycznych i bez uwzględniania wielkości liter. |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | Ciąg musi być zgodny z wyrażeniem regularnym. Zobacz wskazówki dotyczące definiowania wyrażenia regularnego. |
| `required` | `{name:required}` | `Rick` | Służy do wymuszania obecności wartości nieparametryjnej podczas generowania adresu URL |

[!INCLUDE[](~/includes/regex.md)]

Wiele ograniczeń rozdzielanych dwukropek można zastosować do pojedynczego parametru. Na przykład następujące ograniczenie ogranicza parametr do wartości całkowitej 1 lub większej:

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> Ograniczenia trasy, które weryfikują adres URL i są konwertowane na typ CLR, zawsze używają kultury niezmiennej. Na przykład konwersja na `int` typ `DateTime`CLR lub . Te ograniczenia zakładają, że adres URL nie jest zlokalizowany. Ograniczenia trasy dostarczone przez strukturę nie modyfikują wartości przechowywanych w wartościach marszruty. Wszystkie wartości trasy analizowane z adresu URL są przechowywane jako ciągi. Na przykład `float` ograniczenie próbuje przekonwertować wartość trasy na float, ale przekonwertowana wartość jest używana tylko do sprawdzenia, czy można ją przekonwertować na float.

### <a name="regular-expressions-in-constraints"></a>Wyrażenia regularne w ograniczeniach

[!INCLUDE[](~/includes/regex.md)]

Wyrażenia regularne można określić jako ograniczenia `regex(...)` wbudowane przy użyciu ograniczenia trasy. Metody w <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> rodzinie również zaakceptować obiekt literał ograniczeń. Jeśli ten formularz jest używany, wartości ciągu są interpretowane jako wyrażenia regularne.

Poniższy kod używa ograniczenia wyrażenia regularnego w linii wbudowanej:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRegex.cs?name=snippet)]

Poniższy kod używa literału obiektu do określenia ograniczenia wyrażenia regularnego:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRegex2.cs?name=snippet)]

Struktura ASP.NET Core dodaje `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` do konstruktora wyrażeń regularnych. Opis <xref:System.Text.RegularExpressions.RegexOptions> tych członków można znaleźć w części.

Wyrażenia regularne używają ograniczników i tokenów podobnych do tych używanych przez routing i język C#. Tokeny wyrażenia regularnego muszą zostać zmienione. Aby użyć wyrażenia `^\d{3}-\d{2}-\d{4}$` regularnego w ograniczeniu wbudowanym, należy użyć jednej z następujących czynności:

* Zastąp `\` znaki `\\` podane w ciągu jako znaki w `\` pliku źródłowym języka C#, aby uniknąć znaku ucieczki ciągu.
* [Dosłownie literały ciąg .](/dotnet/csharp/language-reference/keywords/string)

Aby uniknąć znaków ogranicznika `}` `[`parametru marszruty `]` `{`, , , `{{` `}}`podwoić znaki w wyrażeniu, na przykład , , `[[`, `]]`. W poniższej tabeli przedstawiono wyrażenie regularne i jego wersję odstępstki:

| Wyrażenie regularne    | Wysunęło się wyrażenie regularne     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

Wyrażenia regularne używane w routingu `^` często zaczynają się od znaku i odpowiadają pozycji początkowej ciągu. Wyrażenia często kończą się `$` znakiem i pasują do końca ciągu. Znaki `^` `$` i upewnij się, że wyrażenie regularne pasuje do wartości całego parametru trasy. Bez `^` znaków `$` i wyrażenie regularne pasuje do dowolnego podciągu w ciągu, który jest często niepożądane. W poniższej tabeli przedstawiono przykłady i wyjaśniono, dlaczego są zgodne lub nie pasują do siebie:

| Wyrażenie   | Ciąg    | Dopasowanie | Komentarz               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | hello     | Tak   | Dopasowania podciągów     |
| `[a-z]{2}`   | 123abc456 | Tak   | Dopasowania podciągów     |
| `[a-z]{2}`   | Mz        | Tak   | Dopasowuje wyrażenie    |
| `[a-z]{2}`   | MZ        | Tak   | Nie z uwzględnieniem wielkości liter    |
| `^[a-z]{2}$` | hello     | Nie    | Zobacz `^` `$` i powyżej |
| `^[a-z]{2}$` | 123abc456 | Nie    | Zobacz `^` `$` i powyżej |

Aby uzyskać więcej informacji na temat składni wyrażeń regularnych, zobacz [.NET Framework Wyrażenia regularne](/dotnet/standard/base-types/regular-expression-language-quick-reference).

Aby ograniczyć parametr do znanego zestawu możliwych wartości, należy użyć wyrażenia regularnego. Na przykład `{action:regex(^(list|get|create)$)}` tylko `action` dopasowuje `list` `get`wartość `create`trasy do , lub . Jeśli przekazywane do słownika ograniczeń, `^(list|get|create)$` ciąg jest równoważne. Ograniczenia, które są przekazywane w słowniku ograniczeń, które nie pasują do jednego ze znanych ograniczeń są również traktowane jako wyrażenia regularne. Ograniczenia przekazywane w szablonie, które nie pasują do jednego ze znanych ograniczeń, nie są traktowane jako wyrażenia regularne.

### <a name="custom-route-constraints"></a>Niestandardowe ograniczenia trasy

Niestandardowe ograniczenia trasy można utworzyć <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> przez implementowanie interfejsu. Interfejs `IRouteConstraint` zawiera <xref:System.Web.Routing.IRouteConstraint.Match*>, `true` który zwraca, jeśli `false` ograniczenie jest spełnione i w inny sposób.

Niestandardowe ograniczenia trasy są rzadko potrzebne. Przed zaimplementowanie ograniczenia trasy niestandardowej należy wziąć pod uwagę alternatywy, takie jak powiązanie modelu.

Aby użyć `IRouteConstraint`niestandardowego, typ ograniczenia trasy musi być <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> zarejestrowany w aplikacji w kontenerze usługi. A `ConstraintMap` to słownik, który mapuje `IRouteConstraint` klucze ograniczenia trasy do implementacji, które sprawdzają te ograniczenia. Aplikacja może `ConstraintMap` być aktualizowana `Startup.ConfigureServices` w ramach [usługi. Wywołanie AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) lub <xref:Microsoft.AspNetCore.Routing.RouteOptions> konfiguracja `services.Configure<RouteOptions>`bezpośrednio z programem . Przykład:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint.cs?name=snippet)]

Poprzednie ograniczenie jest stosowane w następującym kodzie:

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/TestController.cs?name=snippet&highlight=6,13)]

[!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

Implementacja `MyCustomConstraint` zapobiega `0` stosowania do parametru trasy:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint.cs?name=snippet2)]

[!INCLUDE[](~/includes/regex.md)]

Powyższy kod ma następujące działanie:

* Zapobiega `0` w `{id}` segmencie trasy.
* Pokazano, aby zapewnić podstawowy przykład implementacji ograniczenia niestandardowego. Nie należy go używać w aplikacji produkcyjnej.

Poniższy kod jest lepszym podejściem `id` do `0` zapobiegania przetwarzania zawierającego:

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/TestController.cs?name=snippet2)]

Poprzedni kod ma następujące zalety `MyCustomConstraint` w stosunku do podejścia:

* Nie wymaga ograniczenia niestandardowego.
* Zwraca bardziej opisowy błąd, gdy parametr `0`trasy zawiera .

## <a name="parameter-transformer-reference"></a>Odwołanie do transformatora parametrów

Transformatory parametrów:

* Wykonywanie podczas generowania <xref:Microsoft.AspNetCore.Routing.LinkGenerator>łącza przy użyciu programu .
* Zaimplementuj <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer?displayProperty=fullName>plik .
* Są konfigurowane przy użyciu <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>programu .
* Należy wziąć wartość trasy parametru i przekształcić go w nową wartość ciągu.
* Wynik przy użyciu wartości przekształcone w wygenerowanym łączu.

`slugify` Na przykład niestandardowy transformator parametrów we wzorcu `blog\{article:slugify}` trasy z `Url.Action(new { article = "MyTestArticle" })` generuje `blog\my-test-article`.

Należy wziąć `IOutboundParameterTransformer` pod uwagę następujące implementacji:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint2.cs?name=snippet2)]

Aby użyć transformatora parametrów we wzorcu <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> `Startup.ConfigureServices`trasy, skonfiguruj go za pomocą:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint2.cs?name=snippet)]

ASP.NET Core framework używa transformatorów parametrów do przekształcania identyfikatora URI, gdzie punkt końcowy jest rozpoznawany. Na przykład transformatory parametrów przekształcają `area`wartości `controller` `action`trasy `page`używane do dopasowania , , i .

```csharp
routes.MapControllerRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

W poprzednim szablonie trasy `SubscriptionManagementController.GetAll` akcja jest dopasowywała się do identyfikatora URI `/subscription-management/get-all`. Transformator parametrów nie zmienia wartości trasy używanej do generowania łącza. Na przykład `Url.Action("GetAll", "SubscriptionManagement")` wyjścia `/subscription-management/get-all`.

ASP.NET Core udostępnia konwencje interfejsu API do używania transformatorów parametrów z wygenerowanymi trasami:

* Konwencja <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention?displayProperty=fullName> MVC stosuje określony transformator parametrów do wszystkich tras atrybutów w aplikacji. Transformator parametrów przekształca tokeny trasy atrybutu, ponieważ są one zastępowane. Aby uzyskać więcej informacji, zobacz [Używanie transformatora parametrów w celu dostosowania zastępowania tokenów](xref:mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement).
* Razor Pages używa <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention> konwencji interfejsu API. Ta konwencja stosuje określony transformator parametrów do wszystkich automatycznie odnalezionych stron Razor. Transformator parametrów przekształca segmenty folderów i nazw plików tras Razor Pages. Aby uzyskać więcej informacji, zobacz [Dostosowywanie tras stron za pomocą transformatora parametrów](xref:razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes).

<a name="ugr"></a>

## <a name="url-generation-reference"></a>Odwołanie do generowania adresów URL

Ta sekcja zawiera odwołanie do algorytmu zaimplementowanego przez generowanie adresów URL. W praktyce większość złożonych przykładów generowania adresów URL używa kontrolerów lub stron Razor. Zobacz [routing w kontrolerach,](xref:mvc/controllers/routing) aby uzyskać dodatkowe informacje.

Proces generowania adresu URL rozpoczyna się od wywołania [LinkGenerator.GetPathByAddress](xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*) lub podobnej metody. Metoda jest dostarczana z adresem, zestawem wartości trasy i opcjonalnie `HttpContext`informacjami o bieżącym żądaniu od .

Pierwszym krokiem jest użycie adresu do rozpoznania zestawu punktów [`IEndpointAddressScheme<TAddress>`](xref:Microsoft.AspNetCore.Routing.IEndpointAddressScheme`1) końcowych kandydata przy użyciu tego, który pasuje do typu adresu.

Gdy zestaw kandydatów zostanie znaleziony przez schemat adresowy, punkty końcowe są uporządkowane i przetwarzane iteratively do czasu zakończenia operacji generowania adresu URL. Generowanie adresu URL **nie** sprawdza niejednoznaczności, pierwszy zwrócony wynik jest wynikiem końcowym.

### <a name="troubleshooting-url-generation-with-logging"></a>Rozwiązywanie problemów z generowaniem adresów URL za pomocą rejestrowania

Pierwszym krokiem w rozwiązywaniu problemów z generowaniem adresów `Microsoft.AspNetCore.Routing` URL jest ustawienie poziomu rejestrowania na `TRACE`. `LinkGenerator`rejestruje wiele szczegółów na temat jego przetwarzania, które mogą być przydatne do rozwiązywania problemów.

Szczegółowe informacje [na temat](#ugr) generowania adresów URL można znaleźć w informacji o generowaniu adresów URL.

### <a name="addresses"></a>Adresy

Adresy są pojęciem generowania adresów URL używane do powiązania wywołania do generatora łączy z zestawem punktów końcowych kandydata.

Adresy są rozszerzalne koncepcji, które są dostarczane z dwóch implementacji domyślnie:

* Używanie nazwy punktu`string` *końcowego* ( ) jako adresu:
    * Zapewnia podobną funkcjonalność do nazwy trasy MVC.
    * Używa typu <xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata> metadanych.
    * Rozwiązuje podany ciąg względem metadanych wszystkich zarejestrowanych punktów końcowych.
    * Zgłasza wyjątek podczas uruchamiania, jeśli wiele punktów końcowych używa tej samej nazwy.
    * Zalecane do użytku ogólnego poza kontrolerami i stronami Razor.
* Używanie *wartości* <xref:Microsoft.AspNetCore.Routing.RouteValuesAddress>trasy ( ) jako adresu:
    * Zapewnia podobne funkcje do kontrolerów i razor Pages generowania starszych adresów URL.
    * Bardzo skomplikowane, aby rozszerzyć i debugować.
    * Zapewnia implementację `IUrlHelper`używaną przez , Pomocników tagów, pomocników HTML, wyniki akcji itp.

Rolą schematu adresów jest uczynienie skojarzenia między adresem a pasującymi punktami końcowymi według dowolnych kryteriów:

* Schemat nazwy punktu końcowego wykonuje podstawowe wyszukiwanie słownika.
* Schemat wartości trasy ma złożony najlepszy podzbiór algorytmu zestawu.

<a name="ambient"></a>

### <a name="ambient-values-and-explicit-values"></a>Wartości otoczenia i wartości jawne

Z bieżącego żądania routing uzyskuje dostęp do `HttpContext.Request.RouteValues`wartości trasy bieżącego żądania . Wartości skojarzone z bieżącym żądaniem są określane jako **wartości otoczenia**. Dla zapewnienia przejrzystości dokumentacja odnosi się do wartości trasy przekazywanych do metod jako **wartości jawne**.

Poniższy przykład pokazuje wartości otoczenia i jawne wartości. Zawiera wartości otoczenia z bieżącego `{ id = 17, }`żądania i wartości jawne: :

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet)]

Powyższy kod ma następujące działanie:

* Zwraca`/Widget/Index/17`
* Pobiera <xref:Microsoft.AspNetCore.Routing.LinkGenerator> przez [DI](xref:fundamentals/dependency-injection).

Poniższy kod nie zawiera wartości `{ controller = "Home", action = "Subscribe", id = 17, }`otoczenia i wartości jawnych: :

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet2)]

Poprzednia metoda zwraca`/Home/Subscribe/17`

Następujący kod w `WidgetController` `/Widget/Subscribe/17`zwraca:

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet3)]

Poniższy kod zapewnia kontroler z wartości otoczenia w `{ action = "Edit", id = 17, }`bieżącym żądaniu i jawne wartości: :

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/GadgetController.cs?name=snippet)]

Powyższy kod ma następujące działanie:

* `/Gadget/Edit/17`zostanie zwrócona.
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Url>dostaje <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>.
* <xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Action*>   
generuje adres URL ze ścieżką bezwzględną dla metody akcji. Adres URL zawiera `action` określoną nazwę i `route` wartości.

Poniższy kod zawiera wartości otoczenia z `{ page = "./Edit, id = 17, }`bieżącego żądania i wartości jawne: :

[!code-csharp[](routing/samples/3.x/RoutingSample/Pages/Index.cshtml.cs?name=snippet)]

Poprzedni kod ustawia `url` `/Edit/17` się, gdy strona Edytuj razor zawiera następującą dyrektywę strony:

 `@page "{id:int}"`

Jeśli strona Edytuj nie zawiera `"{id:int}"` szablonu `url` `/Edit?id=17`trasy, jest .

Zachowanie MVC <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> dodaje warstwę złożoności oprócz reguł opisanych tutaj:

* `IUrlHelper`zawsze podaje wartości trasy z bieżącego żądania jako wartości otoczenia.
* [IUrlHelper.Action](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Action*) zawsze kopiuje bieżące `action` i `controller` routsycy wartości jako jawne wartości, chyba że zastąpione przez dewelopera.
* [IUrlHelper.Page](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*) zawsze kopiuje bieżącą `page` wartość trasy jako wartość jawną, chyba że zostanie zastąpiona. <!--by the user-->
* `IUrlHelper.Page`zawsze zastępuje bieżącą `handler` wartość `null` trasy jako wartości jawne, chyba że zostanie zastąpiona.

Użytkownicy są często zaskoczeni behawioralne szczegóły wartości otoczenia, ponieważ MVC nie wydaje się przestrzegać własnych zasad. Ze względów historycznych i zgodności niektóre `action`wartości `controller` `page`trasy, `handler` takie jak , , i mają własne zachowanie specjalnego przypadku.

Równoważne funkcje `LinkGenerator.GetPathByAction` dostarczone `LinkGenerator.GetPathByPage` przez i powiela te anomalie `IUrlHelper` dla zgodności.

### <a name="url-generation-process"></a>Proces generowania adresów URL

Po znalezieniu zestawu punktów końcowych kandydata algorytm generowania adresów URL:

* Przetwarza punkty końcowe iteratively.
* Zwraca pierwszy pomyślny wynik.

Pierwszy krok w tym procesie jest nazywany **unieważnienie wartości trasy**.  Unieważnienie wartości trasy jest procesem, w którym routing decyduje, które wartości trasy z wartości otoczenia powinny być używane, a które powinny być ignorowane. Każda wartość otoczenia jest brana pod uwagę i łączona z jawnymi wartościami lub ignorowana.

Najlepszym sposobem, aby myśleć o roli wartości otoczenia jest, że próbują zapisać deweloperów aplikacji wpisując, w niektórych typowych przypadkach. Tradycyjnie scenariusze, w których wartości otoczenia są pomocne są związane z MVC:

* Podczas łączenia z inną akcją w tym samym kontrolerze nie trzeba określać nazwy kontrolera.
* Podczas łączenia z innym kontrolerem w tym samym obszarze nie trzeba określać nazwy obszaru.
* Podczas łączenia z tą samą metodą akcji nie trzeba określać wartości trasy.
* Podczas łączenia z inną częścią aplikacji nie chcesz przenosić wartości trasy, które nie mają znaczenia w tej części aplikacji.

Wywołania `LinkGenerator` `IUrlHelper` lub `null` że powrót są zwykle spowodowane przez nie zrozumienie unieważnienia wartości trasy. Rozwiązywanie problemów z unieważnieniem wartości trasy, jawnie określając więcej wartości trasy, aby sprawdzić, czy to rozwiązuje problem.

Unieważnienie wartości trasy działa przy założeniu, że schemat adresu URL aplikacji jest hierarchiczny, z hierarchią utworzoną od lewej do prawej. Należy wziąć pod `{controller}/{action}/{id?}` uwagę szablon trasy kontrolera podstawowe, aby uzyskać intuicyjne poczucie, jak to działa w praktyce. **Zmiana** wartości **unieważnia** wszystkie wartości trasy, które pojawiają się po prawej stronie. Odzwierciedla to założenie dotyczące hierarchii. Jeśli aplikacja ma wartość `id`otoczenia dla , a operacja `controller`określa inną wartość dla :

* `id`nie będzie ponownie nadużywane, ponieważ `{controller}` jest `{id?}`po lewej stronie .

Kilka przykładów demonstrujących tę zasadę:

* Jeśli jawne wartości zawierają `id`wartość dla `id` , wartość otoczenia dla jest ignorowana. Wartości otoczenia `controller` `action` dla i mogą być używane.
* Jeśli wartości jawne zawierają `action`wartość dla `action` , każda wartość otoczenia dla jest ignorowana. `controller` Można użyć wartości otoczenia. Jeśli jawna `action` wartość dla różni się `action`od `id` wartości otoczenia dla , wartość nie będzie używana.  Jeśli jawna `action` wartość dla jest taka `action`sama `id` jak wartość otoczenia dla , można użyć wartości.
* Jeśli wartości jawne zawierają `controller`wartość dla `controller` , każda wartość otoczenia dla jest ignorowana. Jeśli jawna `controller` wartość dla różni się `controller`od `action` `id` wartości otoczenia dla , i wartości nie będą używane. Jeśli jawna `controller` wartość dla jest taka `controller`sama `action` jak `id` wartość otoczenia dla , i wartości mogą być używane.

Proces ten dodatkowo komplikuje istnienie tras atrybutów i dedykowanych tras konwencjonalnych. Kontroler konwencjonalnych `{controller}/{action}/{id?}` tras, takich jak określenie hierarchii przy użyciu parametrów trasy. W przypadku [dedykowanych tras konwencjonalnych](xref:mvc/controllers/routing#dcr) i [tras atrybutów](xref:mvc/controllers/routing#ar) do kontrolerów i stron Razor:

* Istnieje hierarchia wartości trasy.
* Nie są one wyświetlane w szablonie.

W takich przypadkach generowanie adresu URL definiuje pojęcie **wymaganych wartości.** Punkty końcowe utworzone przez kontrolery i strony Razor mają określone wymagane wartości, które umożliwiają unieważnienie wartości trasy do pracy.

Algorytm unieważnienia wartości trasy w szczegółach:

* Wymagane nazwy wartości są łączone z parametrami trasy, a następnie przetwarzane od lewej do prawej.
* Dla każdego parametru porównywana jest wartość otoczenia i wartość jawna:
    * Jeśli wartość otoczenia i jawne wartości są takie same, proces jest kontynuowany.
    * Jeśli wartość otoczenia jest obecny, a jawna wartość nie jest, wartość otoczenia jest używana podczas generowania adresu URL.
    * Jeśli wartość otoczenia nie jest obecny, a jawna wartość jest, odrzucić wartość otoczenia i wszystkie kolejne wartości otoczenia.
    * Jeśli wartość otoczenia i jawna wartość są obecne, a dwie wartości są różne, odrzucić wartość otoczenia i wszystkie kolejne wartości otoczenia.

W tym momencie operacja generowania adresów URL jest gotowa do oceny ograniczeń trasy. Zestaw zaakceptowanych wartości jest łączony z wartościami domyślnymi parametru, który jest dostarczany do ograniczeń. Jeśli wszystkie ograniczenia przechodzą, operacja jest kontynuowana.

Następnie **zaakceptowane wartości** mogą służyć do rozwijania szablonu trasy. Szablon trasy jest przetwarzany:

* Od lewej do prawej.
* Każdy parametr ma swoją zaakceptowaną wartość podstawioną.
* W następujących szczególnych przypadkach:
  * Jeśli wartości akceptowane brakuje wartości i parametr ma wartość domyślną, używana jest wartość domyślna.
  * Jeśli akceptowane wartości brakuje wartości i parametr jest opcjonalny, przetwarzanie jest kontynuowane.
  * Jeśli dowolny parametr trasy po prawej stronie brakującego parametru opcjonalnego ma wartość, operacja zakończy się niepowodzeniem.
  * <!-- review default-valued parameters optional parameters --> Ciągłe parametry o wartości domyślnej i parametry opcjonalne są zwinięte tam, gdzie to możliwe.

Wartości jawnie pod warunkiem, że nie pasują do segmentu trasy są dodawane do ciągu zapytania. W poniższej tabeli przedstawiono `{controller}/{action}/{id?}`wynik podczas korzystania z szablonu trasy .

| Wartości otoczenia                     | Wartości jawne                        | Wynik                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| sterownik = "Strona główna"                | akcja = "Informacje"                       | `/Home/About`           |
| sterownik = "Strona główna"                | controller = "Kolejność", akcja = "O" | `/Order/About`          |
| sterownik = "Home", kolor = "Czerwony" | akcja = "Informacje"                       | `/Home/About`           |
| sterownik = "Strona główna"                | akcja = "O", kolor = "Czerwony"        | `/Home/About?color=Red` |

### <a name="problems-with-route-value-invalidation"></a>Problemy z unieważnieniem wartości trasy

Od ASP.NET Core 3.0 niektóre schematy generowania adresów URL używane we wcześniejszych wersjach ASP.NET Core nie działają dobrze z generowaniem adresów URL. Zespół ASP.NET Core planuje dodać funkcje, aby zaspokoić te potrzeby w przyszłej wersji. Na razie najlepszym rozwiązaniem jest użycie starszego routingu.

Poniższy kod przedstawia przykład schematu generowania adresów URL, który nie jest obsługiwany przez routing.

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupUnsupported.cs?name=snippet)]

W poprzednim kodzie `culture` parametr trasy jest używany do lokalizacji. Pragnieniem jest, `culture` aby parametr był zawsze akceptowany jako wartość otoczenia. Jednak `culture` parametr nie jest akceptowany jako wartość otoczenia ze względu na sposób działania wymaganych wartości:

* W `"default"` szablonie trasy `culture` parametr trasy znajduje `controller`się po `controller` lewej stronie , `culture`więc zmiany nie unieważniają .
* W `"blog"` szablonie trasy `culture` parametr trasy jest uważany za `controller`prawy po prawej stronie , który pojawia się w wymaganych wartościach.

## <a name="configuring-endpoint-metadata"></a>Konfigurowanie metadanych punktu końcowego

Poniższe łącza zawierają informacje dotyczące konfigurowania metadanych punktu końcowego:

* [Włącz cors z routingiem punktu końcowego](xref:security/cors#enable-cors-with-endpoint-routing)
* [IAuthorizationPolicyProvider próbki](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/CustomPolicyProvider) przy `[MinimumAgeAuthorize]` użyciu atrybutu niestandardowego
* [Testowanie uwierzytelniania za pomocą atrybutu [Autoryzuj]](xref:security/authentication/identity#test-identity)
* <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization*>
* [Wybieranie schematu za pomocą atrybutu [Autoryzuj]](xref:security/authorization/limitingidentitybyscheme#selecting-the-scheme-with-the-authorize-attribute)
* [Stosowanie zasad przy użyciu atrybutu [Autoryzuj]](xref:security/authorization/policies#applying-policies-to-mvc-controllers)
* <xref:security/authorization/roles>

<a name="hostmatch"></a>

## <a name="host-matching-in-routes-with-requirehost"></a>Host dopasowywanie w trasach z RequireHost

<xref:Microsoft.AspNetCore.Builder.RoutingEndpointConventionBuilderExtensions.RequireHost*>stosuje ograniczenie do trasy, która wymaga określonego hosta. Parametrem `RequireHost` [[Host]](xref:Microsoft.AspNetCore.Routing.HostAttribute) może być:

* Host: `www.domain.com`, `www.domain.com` pasuje do dowolnego portu.
* Host z `*.domain.com`symbolami `www.domain.com`wieloznacznych: , mecze , `subdomain.domain.com`lub `www.subdomain.domain.com` na dowolnym porcie.
* Port: `*:5000`, pasuje do portu 5000 z dowolnym hostem.
* Host i `www.domain.com:5000` port: lub `*.domain.com:5000`, dopasowuje hosta i portu.

Można określić wiele `RequireHost` `[Host]`parametrów za pomocą lub . Ograniczenie dopasowuje hosty prawidłowe dla dowolnego z parametrów. Na przykład `[Host("domain.com", "*.domain.com")]` `domain.com`dopasowania `www.domain.com`, `subdomain.domain.com`i .

Następujący kod używa `RequireHost` do wymagania określonego hosta na trasie:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRequireHost.cs?name=snippet)]

Poniższy kod używa `[Host]` atrybutu na kontrolerze, aby wymagać dowolnego z określonych hostów:

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/ProductController.cs?name=snippet)]

Gdy `[Host]` atrybut jest stosowany zarówno do kontrolera, jak i metody akcji:

* Atrybut akcji jest używany.
* Atrybut kontrolera jest ignorowany.

## <a name="performance-guidance-for-routing"></a>Wskazówki dotyczące wydajności routingu

Większość routingu została zaktualizowana w ASP.NET Core 3.0, aby zwiększyć wydajność.

Gdy aplikacja ma problemy z wydajnością, routing jest często podejrzewany jako problem. Podejrzewa się, że routing przyczyny jest to, że struktury, takie jak kontrolery i Razor Pages raport ilość czasu spędzonego wewnątrz struktury w ich rejestrowania wiadomości. Gdy istnieje znacząca różnica między czasem zgłoszonym przez kontrolerów a całkowitym czasem żądania:

* Deweloperzy eliminują swój kod aplikacji jako źródło problemu.
* Często zakłada się, że routing jest przyczyną.

Routing jest testowany pod względem wydajności przy użyciu tysięcy punktów końcowych. Jest mało prawdopodobne, że typowa aplikacja napotka problem z wydajnością tylko przez zbyt duży. Najczęstszą główną przyczyną powolnej wydajności routingu jest zwykle źle zachowujące się niestandardowe oprogramowanie pośredniczące.

W poniższym przykładzie kodu przedstawiono podstawową technikę zawężenia źródła opóźnienia:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupDelay.cs?name=snippet)]

Do routingu czasu:

* Przeplot każdego oprogramowania pośredniczącego z kopią oprogramowania pośredniczącego chronometrażu pokazano w poprzednim kodzie.
* Dodaj unikatowy identyfikator, aby skorelować dane chronometrażu z kodem.

Jest to podstawowy sposób na zawężenie opóźnienia, gdy `10ms`jest to znaczące, na przykład, więcej niż .  `Time 2` Odejmowanie `Time 1` od raportów `UseRouting` czasu spędzonego wewnątrz oprogramowania pośredniczącego.

Poniższy kod używa bardziej kompaktowe podejście do poprzedniego kodu chronometrażu:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupSW.cs?name=snippetSW)]

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupSW.cs?name=snippet)]

### <a name="potentially-expensive-routing-features"></a>Potencjalnie drogie funkcje routingu

Poniższa lista zawiera pewien wgląd w funkcje routingu, które są stosunkowo drogie w porównaniu z podstawowymi szablonami tras:

* Wyrażenia regularne: Istnieje możliwość pisania wyrażeń regularnych, które są złożone lub mają długi czas pracy z niewielką ilością danych wejściowych.

* Segmenty złożone`{x}-{y}-{z}`( ): 
  * Są znacznie droższe niż analizowanie segmentu ścieżki zwykłego adresu URL.
  * Spowodować wiele więcej podciągów są przydzielane.
  * Logika segmentu złożonego nie została zaktualizowana w aktualizacji wydajności routingu core 3.0 w ASP.NET.

* Synchroniczne dostęp do danych: wiele złożonych aplikacji ma dostęp do bazy danych w ramach routingu. ASP.NET Core 2.2 i wcześniejsze routingu może nie zapewnić odpowiednich punktów rozszerzalności do obsługi routingu dostępu do bazy danych. Na przykład <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>i <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> są synchroniczne. Punkty rozszerzalności, <xref:Microsoft.AspNetCore.Routing.MatcherPolicy> takie <xref:Microsoft.AspNetCore.Routing.EndpointSelectorContext> jak i są asynchroniczne.

## <a name="guidance-for-library-authors"></a>Wskazówki dla autorów bibliotek

Ta sekcja zawiera wskazówki dla autorów biblioteki tworzących na routingu. Te szczegóły mają na celu zapewnienie, że deweloperzy aplikacji mają dobre środowisko przy użyciu bibliotek i struktur, które rozszerzają routingu.

### <a name="define-endpoints"></a>Definiowanie punktów końcowych

Aby utworzyć strukturę, która używa routingu do dopasowywania adresów <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>URL, zacznij od zdefiniowania środowiska użytkownika, które tworzy się na wierzchu programu .

**Czy** budować na <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder>szczycie . Dzięki temu użytkownicy mogą skomponować strukturę z innymi funkcjami ASP.NET Core bez pomyłek. Każdy szablon ASP.NET Core zawiera routing. Załóżmy, że routing jest obecny i znany użytkownikom.

```csharp
app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...);

    endpoints.MapHealthChecks("/healthz");
});
```

**DO** zwracać uszczelniony typ `MapMyFramework(...)` betonu <xref:Microsoft.AspNetCore.Builder.IEndpointConventionBuilder>z wywołania do tego implementuje . Większość `Map...` metod struktury postępuj zgodnie z tym wzorcem. Interfejs: `IEndpointConventionBuilder`

* Umożliwia możliwość komponowania metadanych.
* Jest przeznaczony przez różne metody rozszerzenia.

Deklarowanie własnego typu umożliwia dodanie własnych funkcji specyficznych dla struktury do konstruktora. Jest ok, aby zawinąć konstruktora zadeklarowanego przez framework i przekazywać do niego wywołania.

```csharp
app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...).RequrireAuthorization()
                                 .WithMyFrameworkFeature(awesome: true);

    endpoints.MapHealthChecks("/healthz");
});
```

**ROZWAŻ** napisanie <xref:Microsoft.AspNetCore.Routing.EndpointDataSource>własnego . `EndpointDataSource`jest niskim poziomie prymitywne do deklarowania i aktualizowania kolekcji punktów końcowych. `EndpointDataSource`to potężny interfejs API używany przez kontrolery i strony Razor.

Testy routingu mają [podstawowy przykład](https://github.com/aspnet/AspNetCore/blob/master/src/Http/Routing/test/testassets/RoutingSandbox/Framework/FrameworkEndpointDataSource.cs#L17) nieaktuacyjnego źródła danych.

**NIE** próbuj domyślnie `EndpointDataSource` zarejestrować. Wymagaj od użytkowników, <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>aby zarejestrowali swoją strukturę w . Filozofia routingu jest to, że nic `UseEndpoints` nie jest domyślnie uwzględnione i to jest miejsce do rejestrowania punktów końcowych.

### <a name="creating-routing-integrated-middleware"></a>Tworzenie oprogramowania pośredniczącego zintegrowanego z routingu

**NALEŻY ROZWAŻYĆ** definiowanie typów metadanych jako interfejsu.

**DO** umożliwiają użycie typów metadanych jako atrybutu klas i metod.

[!code-csharp[](routing/samples/3.x/RoutingSample/ICoolMetadata.cs?name=snippet2)]

Struktury, takie jak kontrolery i strony Razor obsługują stosowanie atrybutów metadanych do typów i metod. Jeśli zde zadeklarowasz typy metadanych:

* Udostępnij je jako [atrybuty](/dotnet/csharp/programming-guide/concepts/attributes/).
* Większość użytkowników jest zaznajomiona z zastosowaniem atrybutów.

Deklarowanie typu metadanych jako interfejsu dodaje kolejną warstwę elastyczności:

* Interfejsy można komponować.
* Deweloperzy mogą deklarować własne typy, które łączą wiele zasad.

**DO** umożliwiają zastąpienie metadanych, jak pokazano w poniższym przykładzie:

[!code-csharp[](routing/samples/3.x/RoutingSample/ICoolMetadata.cs?name=snippet)]

Najlepszym sposobem, aby postępować zgodnie z tymi wytycznymi jest uniknięcie definiowania **metadanych znaczników:**

* Nie tylko szukać obecności typu metadanych.
* Zdefiniuj właściwość na metadanych i sprawdź właściwość.

Kolekcja metadanych jest uporządkowana i obsługuje zastępowanie według priorytetu. W przypadku kontrolerów metadane metody akcji są najbardziej szczegółowe.

**CZY** oprogramowanie pośredniczące przydatne z i bez routingu.

```csharp
app.UseRouting();

app.UseAuthorization(new AuthorizationPolicy() { ... });

app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...).RequrireAuthorization();
});
```

Jako przykład tej wytycznej należy `UseAuthorization` wziąć pod uwagę oprogramowanie pośredniczące. Oprogramowanie pośredniczące autoryzacji umożliwia przekazywanie w zasadach rezerwowych. <!-- shown where?  (shown here) --> Zasady rezerwowe, jeśli są określone, mają zastosowanie do obu:

* Punkty końcowe bez określonych zasad.
* Żądania, które nie pasują do punktu końcowego.

Dzięki temu oprogramowanie pośredniczące autoryzacji jest przydatne poza kontekstem routingu. Oprogramowanie pośredniczące autoryzacji może być używane do programowania tradycyjnego oprogramowania pośredniczącego.

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Routing jest odpowiedzialny za mapowanie identyfikatorów URI żądań do punktów końcowych i wysyłanie żądań przychodzących do tych punktów końcowych. Trasy są definiowane w aplikacji i konfigurowane po uruchomieniu aplikacji. Trasa może opcjonalnie wyodrębnić wartości z adresu URL zawartego w żądaniu, a te wartości mogą być następnie używane do przetwarzania żądań. Korzystając z informacji o trasie z aplikacji, routing jest również w stanie wygenerować adresy URL, które mapują do punktów końcowych.

Aby użyć najnowszych scenariuszy routingu w ASP.NET Core 2.2, określ [wersję](xref:mvc/compatibility-version) zgodności `Startup.ConfigureServices`z rejestracją usług MVC w:

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

Opcja <xref:Microsoft.AspNetCore.Mvc.MvcOptions.EnableEndpointRouting> określa, czy routing powinien wewnętrznie używać logiki <xref:Microsoft.AspNetCore.Routing.IRouter>opartej na punkcie końcowym lub logiki opartej na ASP.NET Core 2.1 lub wcześniejszej. Gdy wersja zgodności jest ustawiona na 2.2 lub `true`nowszą, wartością domyślną jest . Ustaw wartość, `false` aby użyć wcześniejszej logiki routingu:

```csharp
// Use the routing logic of ASP.NET Core 2.1 or earlier:
services.AddMvc(options => options.EnableEndpointRouting = false)
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

Aby uzyskać <xref:Microsoft.AspNetCore.Routing.IRouter>więcej informacji na temat routingu opartego na tym temacie, zobacz [ASP.NET wersja core 2.1 tego tematu](/aspnet/core/fundamentals/routing?view=aspnetcore-2.1).

> [!IMPORTANT]
> Ten dokument obejmuje routing ASP.NET niskiego poziomu. Aby uzyskać informacje na temat ASP.NET routingu Core MVC, zobacz <xref:mvc/controllers/routing>. Aby uzyskać informacje na temat konwencji routingu na stronach Razor Pages, zobacz <xref:razor-pages/razor-pages-conventions>.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="routing-basics"></a>Podstawowe informacje o routingu

Większość aplikacji powinna wybrać podstawowy i opisowy schemat routingu, aby adresy URL były czytelne i znaczące. Domyślna trasa `{controller=Home}/{action=Index}/{id?}`konwencjonalna:

* Obsługuje podstawowy i opisowy schemat routingu.
* Jest użytecznym punktem wyjścia dla aplikacji opartych na interfejsie użytkownika.

Deweloperzy często dodają dodatkowe trasy terse do obszarów o dużym natężeniu ruchu aplikacji w sytuacjach specjalistycznych przy użyciu [routingu atrybutów](xref:mvc/controllers/routing#attribute-routing) lub dedykowanych tras konwencjonalnych. Przykłady sytuacji specjalistycznych obejmują punkty końcowe bloga i e-commerce.

Interfejsy API sieci Web powinny używać routingu atrybutów do modelowania funkcji aplikacji jako zestawu zasobów, w których operacje są reprezentowane przez zlecenia HTTP. Oznacza to, że wiele operacji, na przykład GET i POST, na tym samym zasobie logicznym używać tego samego adresu URL. Routing atrybutów zapewnia poziom kontroli, który jest potrzebny do starannego projektowania układu publicznego punktu końcowego interfejsu API.

Aplikacje Razor Pages używają domyślnego routingu konwencjonalnego do obsługi nazwanych zasobów w folderze *Strony* aplikacji. Dostępne są dodatkowe konwencje, które umożliwiają dostosowanie zachowania routingu stron Razor. Aby uzyskać więcej informacji, zobacz <xref:razor-pages/index> i <xref:razor-pages/razor-pages-conventions>.

Obsługa generowania adresów URL umożliwia tworzenie aplikacji bez twardych adresów URL kodowania, aby połączyć aplikację ze sobą. Ta obsługa umożliwia rozpoczęcie podstawowej konfiguracji routingu i modyfikowanie tras po określeniu układu zasobów aplikacji.

Routing używa *punktów* końcowych`Endpoint`( ) do reprezentowania logicznych punktów końcowych w aplikacji.

Punkt końcowy definiuje delegata do przetwarzania żądań i zbiór dowolnych metadanych. Metadane są używane implementują przekrojowe problemy oparte na zasadach i konfiguracji dołączonej do każdego punktu końcowego.

System wyznaczania tras ma następujące właściwości:

* Składnia szablonu trasy służy do definiowania tras z tokenizowanymi parametrami trasy.
* Konfiguracja punktu końcowego w stylu konwencjonalnym i atrybutu jest dozwolona.
* <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>służy do określenia, czy parametr URL zawiera prawidłową wartość dla danego ograniczenia punktu końcowego.
* Modele aplikacji, takie jak MVC/Razor Pages, rejestrują wszystkie swoje punkty końcowe, które mają przewidywalną implementację scenariuszy routingu.
* Implementacja routingu sprawia, że decyzje routingu w dowolnym miejscu w potoku oprogramowania pośredniczącego.
* Oprogramowanie pośredniczące, które pojawia się po oprogramowaniu pośredniczącym routingu można sprawdzić wynik decyzji punktu końcowego oprogramowania pośredniczącego routingu dla danego identyfikatora URI żądania.
* Jest możliwe do wyliczenia wszystkich punktów końcowych w aplikacji w dowolnym miejscu w potoku oprogramowania pośredniczącego.
* Aplikacja może używać routingu do generowania adresów URL (na przykład przekierowania lub łączy) na podstawie informacji o punkcie końcowym, a tym samym unikać zakodowanych adresów URL, co pomaga w utrzymaniu.
* Generowanie adresów URL opiera się na adresach, które obsługują dowolną rozszerzalność:

  * Interfejs API generatora łączy (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>) można rozpoznać w dowolnym miejscu za pomocą [iniekcji zależności (DI)](xref:fundamentals/dependency-injection) do generowania adresów URL.
  * Jeśli interfejs API generatora łączy nie <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> jest dostępny za pośrednictwem di, oferuje metody tworzenia adresów URL.

> [!NOTE]
> Wraz z wydaniem routingu punktu końcowego w ASP.NET Core 2.2 łączenie punktów końcowych jest ograniczone do akcji i stron stron MVC/Razor Pages. Rozszerzenia możliwości łączenia punktów końcowych są planowane dla przyszłych wydań.

Routing jest połączony z potoku <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> [oprogramowania pośredniczącego](xref:fundamentals/middleware/index) przez klasę. [ASP.NET Core MVC](xref:mvc/overview) dodaje routing do potoku oprogramowania pośredniczącego w ramach swojej konfiguracji i obsługuje routing w aplikacjach MVC i Razor Pages. Aby dowiedzieć się, jak używać routingu jako składnika autonomicznego, zobacz sekcję [Użyj oprogramowania pośredniczącego routingu.](#use-routing-middleware)

### <a name="url-matching"></a>Dopasowywanie adresów URL

Dopasowywanie adresów URL jest procesem, w którym routing wysyła żądanie przychodzące do *punktu końcowego*. Ten proces jest oparty na danych w ścieżce adresu URL, ale można go rozszerzyć, aby uwzględnić wszystkie dane w żądaniu. Możliwość wysyłania żądań do oddzielnych programów obsługi jest kluczem do skalowania rozmiaru i złożoności aplikacji.

System routingu w routingu punktu końcowego jest odpowiedzialny za wszystkie decyzje dotyczące wysyłki. Ponieważ oprogramowanie pośredniczące stosuje zasady oparte na wybranym punkcie końcowym, ważne jest, aby każda decyzja, która może mieć wpływ na wysyłkę lub stosowanie zasad zabezpieczeń, została podjęta wewnątrz systemu routingu.

Po wykonaniu delegata punktu końcowego właściwości [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) są ustawione na odpowiednie wartości na podstawie przetwarzania żądań wykonywane do tej pory.

[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) jest słownikiem *wartości trasy* wytwarzanych z trasy. Wartości te są zwykle określane przez tokenizacji adresu URL i może służyć do akceptowania danych wejściowych użytkownika lub do podejmowania dalszych decyzji wysyłki wewnątrz aplikacji.

[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) jest workiem właściwości dodatkowych danych związanych z dopasowaną trasą. <xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*>są dostarczane do obsługi kojarzenia danych o stanie z każdej trasy, dzięki czemu aplikacja może podejmować decyzje na podstawie trasy dopasowane. Te wartości są zdefiniowane przez dewelopera i **nie** wpływają na zachowanie routingu w żaden sposób. Ponadto wartości ukryte w [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) mogą być dowolnego typu, w przeciwieństwie do [RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values), które muszą być konwertowane do i z ciągów.

[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) to lista tras, które brały udział w pomyślnym dopasowaniu żądania. Trasy mogą być zagnieżdżone wewnątrz siebie. Właściwość <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> odzwierciedla ścieżkę przez drzewo logiczne tras, które doprowadziły do dopasowania. Ogólnie rzecz biorąc pierwszy <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> element w jest zbieranie trasy i powinny być używane do generowania adresów URL. Ostatni element <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> w jest program obsługi marszruty, który pasuje.

<a name="lg"></a>

### <a name="url-generation-with-linkgenerator"></a>Generowanie adresów URL za pomocą linkgeneratora

Generowanie adresów URL to proces, w którym routing może tworzyć ścieżkę adresu URL na podstawie zestawu wartości trasy. Pozwala to na logiczne oddzielenie punktów końcowych i adresów URL, które uzyskują do nich dostęp.

Routing punktu końcowego obejmuje<xref:Microsoft.AspNetCore.Routing.LinkGenerator>interfejs API generatora łączy ( ). <xref:Microsoft.AspNetCore.Routing.LinkGenerator>jest usługą singleton, którą można pobrać z [DI](xref:fundamentals/dependency-injection). Interfejs API może służyć poza kontekstem żądania wykonywania. MVC <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> i scenariusze, które <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>opierają się na , takich jak [Pomocników tagów,](xref:mvc/views/tag-helpers/intro)Pomocników HTML i [wyniki akcji](xref:mvc/controllers/actions), użyj generatora łączy, aby zapewnić możliwości generowania łączy.

Generator łączy jest wspierany przez koncepcję systemów *adresów* i *adresów*. Schemat adresów jest sposobem określania punktów końcowych, które powinny być brane pod uwagę dla generowania łączy. Na przykład scenariusze wartości nazwy trasy i trasy, które wielu użytkowników zna ze stron MVC/Razor, są implementowane jako schemat adresowy.

Generator łączy może łączyć się z akcjami i stronami MVC/Razor Pages za pomocą następujących metod rozszerzenia:

* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*>
* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetPathByPage*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetUriByPage*>

Przeciążenie tych metod akceptuje argumenty, `HttpContext`które zawierają . Metody te są funkcjonalnie równoważne `Url.Action` i `Url.Page` oferują dodatkową elastyczność i opcje.

Metody `GetPath*` są najbardziej `Url.Action` podobne `Url.Page` do i w tym generują identyfikator URI zawierający ścieżkę bezwzględną. Metody `GetUri*` zawsze generują bezwzględny identyfikator URI zawierający schemat i host. Metody, które `HttpContext` akceptują generowanie identyfikatora URI w kontekście żądania wykonywania. Wartości trasy otoczenia, ścieżka bazowa adresu URL, schemat i host z żądania wykonywania są używane, chyba że zostaną zastąpione.

<xref:Microsoft.AspNetCore.Routing.LinkGenerator>nazywa się z adresem. Generowanie identyfikatora URI odbywa się w dwóch etapach:

1. Adres jest powiązany z listą punktów końcowych, które pasują do adresu.
1. Każdy punkt końcowy `RoutePattern` jest obliczany, dopóki nie zostanie znaleziony wzorzec trasy, który pasuje do podanych wartości. Wynikowe wyjście jest łączone z innymi częściami URI dostarczonymi do generatora łączy i zwróconymi.

Metody zapewniane <xref:Microsoft.AspNetCore.Routing.LinkGenerator> przez obsługę standardowych możliwości generowania łączy dla dowolnego typu adresu. Najwygodniejszym sposobem użycia generatora łączy są metody rozszerzenia, które wykonują operacje dla określonego typu adresu.

| Metoda rozszerzenia   | Opis                                                         |
| ------------------ | ------------------------------------------------------------------- |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | Generuje identyfikator URI ze ścieżką bezwzględną na podstawie podanych wartości. |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | Generuje bezwzględny identyfikator URI na podstawie podanych wartości.             |

> [!WARNING]
> Należy zwrócić uwagę na następujące <xref:Microsoft.AspNetCore.Routing.LinkGenerator> implikacje metod wywoływania:
>
> * Użyj `GetUri*` metod rozszerzenia z ostrożnością w konfiguracji aplikacji, która nie sprawdza poprawności `Host` nagłówka żądań przychodzących. Jeśli `Host` nagłówek żądań przychodzących nie jest zweryfikowany, niezaufane dane wejściowe żądania mogą być wysyłane z powrotem do klienta w identyfikatorach URI w widoku/stronie. Zaleca się, aby wszystkie aplikacje produkcyjne `Host` skonfigurować swój serwer, aby sprawdzić poprawność nagłówka względem znanych prawidłowych wartości.
>
> * Należy <xref:Microsoft.AspNetCore.Routing.LinkGenerator> zachować ostrożność w `Map` oprogramowaniu pośredniczącym w połączeniu z programem lub . `MapWhen` `Map*`zmienia ścieżkę podstawową żądania wykonywania, co wpływa na dane wyjściowe generowania łączy. Wszystkie interfejsy <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API umożliwiają określanie ścieżki podstawowej. Zawsze należy określić pustą `Map*`ścieżkę bazową, aby cofnąć wpływ na generowanie łączy.

## <a name="differences-from-earlier-versions-of-routing"></a>Różnice w stosunku do wcześniejszych wersji routingu

Istnieje kilka różnic między routingiem punktu końcowego w ASP.NET Core 2.2 lub nowszych i wcześniejszych wersjach routingu w ASP.NET Core:

* System routingu punktu końcowego <xref:Microsoft.AspNetCore.Routing.IRouter>nie obsługuje rozszerzalności opartej <xref:Microsoft.AspNetCore.Routing.Route>na punktach końcowych, w tym dziedziczenia po .

* Routing punktu końcowego nie obsługuje [programu WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim). Użyj [wersji zgodności 2.1](xref:mvc/compatibility-version) `.SetCompatibilityVersion(CompatibilityVersion.Version_2_1)`( ), aby kontynuować korzystanie z podkładki zgodności.

* Routing punktu końcowego ma inne zachowanie dla wielkości liter y wygenerowane identyfikatory URI podczas korzystania z konwencjonalnych tras.

  Rozważmy następujący domyślny szablon trasy:

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  Załóżmy, że łącze do akcji jest generowane przy użyciu następującej trasy:

  ```csharp
  var link = Url.Action("ReadPost", "blog", new { id = 17, });
  ```

  Za <xref:Microsoft.AspNetCore.Routing.IRouter>pomocą routingu opartego na tym `/blog/ReadPost/17`kodzie generuje identyfikator URI , który respektuje wielkość liter podanej wartości trasy. Routing punktu końcowego w ASP.NET core 2.2 `/Blog/ReadPost/17` lub nowszych produkuje ("Blog" jest kapitalizowany). Routing punktu końcowego `IOutboundParameterTransformer` udostępnia interfejs, który może służyć do dostosowywania tego zachowania globalnie lub do stosowania różnych konwencji do mapowania adresów URL.

  Aby uzyskać więcej informacji, zobacz [sekcję Odwołanie do transformatora parametrów.](#parameter-transformer-reference)

* Generowanie łączy używane przez strony MVC/Razor ze konwencjonalnymi trasami zachowuje się inaczej podczas próby połączenia z kontrolerem/działaniem lub stroną, która nie istnieje.

  Rozważmy następujący domyślny szablon trasy:

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  Załóżmy, że łącze do akcji jest generowane przy użyciu szablonu domyślnego z następującymi elementami:

  ```csharp
  var link = Url.Action("ReadPost", "Blog", new { id = 17, });
  ```

  Z `IRouter`routingu opartego na `/Blog/ReadPost/17`podstawie, `BlogController` wynik jest zawsze , nawet `ReadPost` jeśli nie istnieje lub nie ma metody akcji. Zgodnie z oczekiwaniami routing punktu końcowego w ASP.NET Core 2.2 lub nowszych `/Blog/ReadPost/17` powoduje, że istnieje metoda akcji. *Jednak routing punktu końcowego tworzy pusty ciąg, jeśli akcja nie istnieje.* Koncepcyjnie routing punktu końcowego nie zakłada, że punkt końcowy istnieje, jeśli akcja nie istnieje.

* *Algorytm unieważnienia wartości otoczenia generowania* łącza zachowuje się inaczej, gdy jest używany z routingiem punktu końcowego.

  *Unieważnienie wartości otoczenia* jest algorytmem, który decyduje, które wartości trasy z aktualnie wykonywanego żądania (wartości otoczenia) mogą być używane w operacjach generowania łączy. Routing konwencjonalny zawsze unieważniał dodatkowe wartości trasy podczas łączenia z inną akcją. Routing atrybutów nie ma tego zachowania przed wydaniem ASP.NET Core 2.2. We wcześniejszych wersjach ASP.NET Core łącza do innej akcji, które używają tych samych nazw parametrów trasy, powodowały błędy generowania łączy. W ASP.NET Core 2.2 lub nowszym obie formy routingu unieważniają wartości podczas łączenia z inną akcją.

  Rozważmy poniższy przykład w ASP.NET Core 2.1 lub wcześniejszych. Podczas łączenia z inną akcją (lub inną stroną) wartości trasy mogą być ponownie zaużywane w niepożądany sposób.

  W */Pages/Store/Product.cshtml*:

  ```cshtml
  @page "{id}"
  @Url.Page("/Login")
  ```

  W */Pages/Login.cshtml*:

  ```cshtml
  @page "{id?}"
  ```

  Jeśli identyfikator URI znajduje się `/Store/Product/18` w ASP.NET Core 2.1 lub starszym, łącze wygenerowane na stronie Sklep/Informacje `@Url.Page("/Login")` przez to . `/Login/18` Wartość `id` 18 jest ponownie, mimo że miejsce docelowe łącza jest zupełnie inną częścią aplikacji. Wartość `id` trasy w kontekście `/Login` strony jest prawdopodobnie wartością identyfikatora użytkownika, a nie wartością identyfikatora produktu sklepu.

  W routingu punktu końcowego z ASP.NET Core 2.2 `/Login`lub nowszym wynikiem jest . Wartości otoczenia nie są ponownie używany, gdy połączone miejsce docelowe jest inną akcją lub stroną.

* Składnia parametru trasy zaokrąglania: Ukośniki do przodu nie są`**`kodowane podczas używania dwustyguszki ( ) składni parametru catch-all.

  Podczas generowania łączy system routingu koduje wartość przechwyconą w`**`podwójnej gwiazdki ( `{**myparametername}`) parametr catch-all (na przykład) z wyjątkiem ukośników do przodu. Połowa z podwójną gwiazdką jest obsługiwana z `IRouter`routingiem opartym na ASP.NET Core 2.2 lub nowszym.

  Składnia parametrów pojedynczej gwiazdki catch-all w poprzednich wersjach`{*myparametername}`ASP.NET Core ( ) pozostaje obsługiwana, a ukośniki do przodu są zakodowane.

  | Trasa              | Łącze wygenerowane za pomocą<br>`Url.Action(new { category = "admin/products" })`&hellip; |
  | ------------------ | --------------------------------------------------------------------- |
  | `/search/{*page}`  | `/search/admin%2Fproducts`(ukośnik do przodu jest zakodowany)             |
  | `/search/{**page}` | `/search/admin/products`                                              |

### <a name="middleware-example"></a>Przykład oprogramowania pośredniczącego

W poniższym przykładzie oprogramowanie pośredniczące używa <xref:Microsoft.AspNetCore.Routing.LinkGenerator> interfejsu API do utworzenia łącza do metody akcji, która zawiera listę produktów magazynu. Za pomocą generatora łączy przez wstrzyknięcie `GenerateLink` go do klasy i wywołanie jest dostępna dla każdej klasy w aplikacji.

```csharp
using Microsoft.AspNetCore.Routing;

public class ProductsLinkMiddleware
{
    private readonly LinkGenerator _linkGenerator;

    public ProductsLinkMiddleware(RequestDelegate next, LinkGenerator linkGenerator)
    {
        _linkGenerator = linkGenerator;
    }

    public async Task InvokeAsync(HttpContext httpContext)
    {
        var url = _linkGenerator.GetPathByAction("ListProducts", "Store");

        httpContext.Response.ContentType = "text/plain";

        await httpContext.Response.WriteAsync($"Go to {url} to see our products.");
    }
}
```

### <a name="create-routes"></a>Tworzenie tras

Większość aplikacji tworzy <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> trasy przez wywołanie lub <xref:Microsoft.AspNetCore.Routing.IRouteBuilder>jedną z podobnych metod rozszerzenia zdefiniowanych w programie . Każda z <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> metod rozszerzenia utworzyć <xref:Microsoft.AspNetCore.Routing.Route> wystąpienie i dodać go do kolekcji trasy.

<xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>nie akceptuje parametru obsługi marszruty. <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>dodaje tylko trasy obsługiwane przez <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>plik . Aby dowiedzieć się więcej o <xref:mvc/controllers/routing>routingu w MVC, zobacz .

Poniższy przykład kodu jest <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> przykładem wywołania używanego przez typową definicję trasy core MVC ASP.NET:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Ten szablon pasuje do ścieżki adresu URL i wyodrębnia wartości trasy. Na przykład ścieżka `/Products/Details/17` generuje następujące wartości `{ controller = Products, action = Details, id = 17 }`trasy: .

Wartości trasy są określane przez podzielenie ścieżki adresu URL na segmenty i dopasowanie każdego segmentu do nazwy *parametru trasy* w szablonie trasy. Parametry trasy są nazwane. Parametry zdefiniowane przez załączenie nazwy `{ ... }`parametru w nawiasy klamrowe .

Poprzedni szablon może również odpowiadać `/` ścieżce `{ controller = Home, action = Index }`adresu URL i tworzyć wartości . Dzieje się `{controller}` tak, ponieważ parametry `{action}` i `id` trasy mają wartości domyślne, a parametr trasy jest opcjonalny. Znak równości (`=`), po którym następuje wartość po nazwie parametru trasy definiuje wartość domyślną dla parametru. Znak zapytania`?`( ) po nazwie parametru trasy definiuje parametr opcjonalny.

Parametry trasy z wartością domyślną *zawsze* generują wartość trasy, gdy trasa jest zgodna. Parametry opcjonalne nie generują wartości trasy, jeśli nie ma odpowiedniego segmentu ścieżki adresu URL. Zobacz sekcję [odwołania do szablonu trasy,](#route-template-reference) aby uzyskać dokładny opis scenariuszy szablonu trasy i składni.

W poniższym przykładzie definicja `{id:int}` parametru trasy definiuje ograniczenie [trasy](#route-constraint-reference) dla parametru `id` trasy:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

Ten szablon pasuje do `/Products/Details/17` ścieżki `/Products/Details/Apples`adresu URL, takiej jak, ale nie . Ograniczenia trasy <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> implementują i sprawdzają wartości trasy, aby je zweryfikować. W tym przykładzie `id` wartość trasy musi być konwertowana na całkowitą. Zobacz [route-constraint-reference, aby](#route-constraint-reference) uzyskać wyjaśnienie ograniczeń trasy dostarczonych przez strukturę.

Dodatkowe przeciążenia <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> wartości `constraints`akceptowania dla , `dataTokens`i `defaults`. Typowym zastosowaniem tych parametrów jest przekazanie anonimowo wpisanego obiektu, w którym nazwy właściwości typu anonimowego odpowiadają nazwom parametrów trasy.

Poniższe <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> przykłady tworzą równoważne trasy:

```csharp
routes.MapRoute(
    name: "default_route",
    template: "{controller}/{action}/{id?}",
    defaults: new { controller = "Home", action = "Index" });

routes.MapRoute(
    name: "default_route",
    template: "{controller=Home}/{action=Index}/{id?}");
```

> [!TIP]
> Składnia wbudowana do definiowania ograniczeń i wartości domyślnych może być wygodna w przypadku prostych tras. Istnieją jednak scenariusze, takie jak tokeny danych, które nie są obsługiwane przez składnię wbudowaną.

W poniższym przykładzie przedstawiono kilka dodatkowych scenariuszy:

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{**article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

Poprzedni szablon pasuje do ścieżki `/Blog/All-About-Routing/Introduction` adresu URL, `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`takiej jak i wyodrębnia wartości . Domyślne wartości `controller` trasy `action` dla i są tworzone przez trasę, nawet jeśli w szablonie nie ma odpowiednich parametrów trasy. Wartości domyślne można określić w szablonie trasy. Parametr `article` trasy jest definiowany jako *catch-all* przez pojawienie się`**`podwójnej gwiazdki ( ) przed nazwą parametru trasy. Parametry trasy catch-all przechwytują pozostałą część ścieżki adresu URL i mogą również odpowiadać pustejasce.

W poniższym przykładzie dodano ograniczenia trasy i tokeny danych:

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

Poprzedni szablon pasuje do ścieżki `/en-US/Products/5` adresu URL, `{ controller = Products, action = Details, id = 5 }` takiej jak `{ locale = en-US }`i wyodrębnia wartości i tokeny danych.

![Lokalne tokeny systemu Windows](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a>Generowanie adresu URL klasy trasy

Klasa <xref:Microsoft.AspNetCore.Routing.Route> może również wykonywać generowanie adresów URL, łącząc zestaw wartości trasy z szablonem trasy. Jest to logicznie odwrotny proces dopasowywania ścieżki adresu URL.

> [!TIP]
> Aby lepiej zrozumieć generowanie adresów URL, wyobraź sobie, jaki adres URL chcesz wygenerować, a następnie zastanów się, jak szablon trasy będzie zgodny z tym adresem URL. Jakie wartości powstałyby? Jest to odpowiednik przybliżone, <xref:Microsoft.AspNetCore.Routing.Route> jak działa generowanie adresów URL w klasie.

W poniższym przykładzie użyto ogólnej trasy domyślnej ASP.NET Core MVC:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Z wartościami `{ controller = Products, action = List }`trasy `/Products/List` generowany jest adres URL. Wartości trasy są zastępowane odpowiednimi parametrami trasy w celu utworzenia ścieżki adresu URL. Ponieważ `id` jest opcjonalnym parametrem trasy, adres URL `id`jest pomyślnie generowany bez wartości dla .

Z wartościami `{ controller = Home, action = Index }`trasy `/` generowany jest adres URL. Podane wartości trasy są zgodne z wartościami domyślnymi, a segmenty odpowiadające wartościom domyślnym są bezpiecznie pomijane.

Oba adresy URL wygenerowane w obie`/Home/Index` `/`strony z następującą definicją trasy ( i ) generują te same wartości trasy, które zostały użyte do wygenerowania adresu URL.

> [!NOTE]
> Aplikacja używająca ASP.NET Core MVC <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> powinna używać do generowania adresów URL zamiast wywoływania bezpośrednio do routingu.

Aby uzyskać więcej informacji na temat generowania adresów URL, zobacz sekcję [odwołanie do generowania adresów URL.](#url-generation-reference)

## <a name="use-routing-middleware"></a>Korzystanie z oprogramowania pośredniczącego routingu

Odwołanie się do [metapakietu Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) w pliku projektu aplikacji.

Dodaj routing do kontenera usługi w: `Startup.ConfigureServices`

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

Trasy muszą być skonfigurowane w metodzie. `Startup.Configure` Przykładowa aplikacja używa następujących interfejsów API:

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>&ndash; Pasuje tylko do żądań HTTP GET.
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

W poniższej tabeli przedstawiono odpowiedzi z podanymi identyfikatorami URI.

| Identyfikator URI                    | Odpowiedź                                          |
| ---------------------- | ------------------------------------------------- |
| `/package/create/3`    | Cześć! Wartości trasy: [operacja, tworzenie], [id, 3] |
| `/package/track/-3`    | Cześć! Wartości trasy: [operacja, ścieżka], [id, -3] |
| `/package/track/-3/`   | Cześć! Wartości trasy: [operacja, ścieżka], [id, -3] |
| `/package/track/`      | Żądanie przechodzi, bez dopasowania.              |
| `GET /hello/Joe`       | Cześć, Joe!                                          |
| `POST /hello/Joe`      | Żądanie przechodzi, pasuje tylko do protokołu HTTP GET. |
| `GET /hello/Joe/Smith` | Żądanie przechodzi, bez dopasowania.              |

Struktura zawiera zestaw metod rozszerzenia do tworzenia<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>tras ( ):

* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareVerb*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>

Metody `Map[Verb]` używają ograniczeń, aby ograniczyć trasę do zlecenia HTTP w nazwie metody. Na przykład <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> zobacz <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>i .

## <a name="route-template-reference"></a>Odwołanie do szablonu trasy

Tokeny w nawiasach`{ ... }`klamrowych ( ) definiują *parametry trasy,* które są powiązane, jeśli trasa jest dopasowywała. Można zdefiniować więcej niż jeden parametr trasy w segmencie trasy, ale muszą one być oddzielone wartością literału. Na przykład `{controller=Home}{action=Index}` nie jest prawidłową trasą, ponieważ nie `{controller}` ma `{action}`wartości literału między i . Te parametry trasy muszą mieć nazwę i mogą mieć określone dodatkowe atrybuty.

Tekst dosłowny inny niż `{id}`parametry trasy (na `/` przykład) i separator ścieżki muszą być zgodne z tekstem w adresie URL. Dopasowanie tekstu jest niewrażliwe na rozmiary i opiera się na zdekodowanym reprezentacji ścieżki adresów URL. `{` Aby dopasować dosłowny ogranicznik parametru trasy ( lub `}``{{` ), `}}`wyjdź z ogranicznika, powtarzając znak ( lub ).

Wzorce adresów URL, które próbują przechwycić nazwę pliku z opcjonalnym rozszerzeniem pliku, mają dodatkowe uwagi. Rozważmy na przykład `files/{filename}.{ext?}`szablon . Gdy wartości `filename` dla `ext` obu i istnieją, obie wartości są wypełniane. Jeśli w adresie URL `filename` istnieje tylko wartość, trasa jest`.`zgodna, ponieważ okres końcowy ( ) jest opcjonalny. Następujące adresy URL są zgodne z tą trasą:

* `/files/myFile.txt`
* `/files/myFile`

Gwiazdki (`*`) lub podwójnej gwiazdki`**`( ) jako prefiksu parametru trasy do powiązania z resztą identyfikatora URI. Są one nazywane *catch-all* parametrów. Na przykład `blog/{**slug}` dopasowuje dowolny identyfikator `/blog` URI, który rozpoczyna się od `slug` i ma dowolną wartość po nim, która jest przypisana do wartości trasy. Catch-all parametry mogą również odpowiadać pusty ciąg.

Parametr catch-all wycina odpowiednie znaki, gdy trasa jest używana do`/`generowania adresu URL, w tym separatora ścieżki ( ) znaków. Na przykład trasa `foo/{*path}` z `{ path = "my/path" }` wartościami `foo/my%2Fpath`trasy generuje . Zwróć uwagę na ukucie do przodu. Aby znaki separatora ścieżki w `**` obie strony, należy użyć prefiksu parametru trasy. Trasa `foo/{**path}` z `{ path = "my/path" }` generuje `foo/my/path`.

Parametry trasy mogą mieć *wartości domyślne* wyznaczone przez określenie wartości domyślnej po`=`nazwie parametru oddzielonej znakiem równości ( ). Na przykład `{controller=Home}` definiuje `Home` jako wartość `controller`domyślną dla . Wartość domyślna jest używana, jeśli w adresie URL parametru nie ma żadnej wartości. Parametry trasy są opcjonalne, dołączając`?`znak zapytania ( ) na końcu `id?`nazwy parametru, jak w . Różnica między wartościami opcjonalnymi a domyślnymi parametrami trasy polega na&mdash;tym, że parametr trasy z wartością domyślną zawsze tworzy wartość, którą parametr opcjonalny ma wartość tylko wtedy, gdy wartość jest dostarczana przez adres URL żądania.

Parametry trasy mogą mieć ograniczenia, które muszą odpowiadać wartości trasy powiązanej z adresem URL. Dodanie dwukropka (`:`) i nazwy ograniczenia po nazwie parametru trasy określa ograniczenie *wbudowane* dla parametru trasy. Jeśli ograniczenie wymaga argumentów, są one ujęte`(...)`w nawiasy ( ) po nazwie ograniczenia. Można określić wiele ograniczeń wbudowanych, dołączając`:`inny dwukropek ( ) i nazwę ograniczenia.

Nazwa ograniczenia i argumenty są <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> przekazywane do usługi, aby utworzyć wystąpienie do użycia w przetwarzaniu adresów <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> URL. Na przykład szablon `blog/{article:minlength(10)}` trasy określa `minlength` ograniczenie z `10`argumentem . Aby uzyskać więcej informacji na temat ograniczeń trasy i listy ograniczeń przewidzianych przez strukturę, zobacz [sekcja Odwołanie do ograniczenia trasy.](#route-constraint-reference)

Parametry trasy mogą również mieć transformatory parametrów, które przekształcają wartość parametru podczas generowania łączy i dopasowywania akcji i stron do adresów URL. Podobnie jak wiązania, transformatory parametrów można dodać w linii`:`do parametru trasy, dodając dwukropek ( ) i nazwę transformatora po nazwie parametru trasy. Na przykład szablon `blog/{article:slugify}` trasy określa `slugify` transformator. Aby uzyskać więcej informacji na temat transformatorów parametrów, zobacz [sekcję Odwołanie do transformatora parametrów.](#parameter-transformer-reference)

W poniższej tabeli przedstawiono przykładowe szablony tras i ich zachowanie.

| Szablon trasy                           | Przykładowy pasujący identyfikator URI    | Identyfikator URI żądania&hellip;                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | Pasuje tylko do `/hello`pojedynczej ścieżki .                                     |
| `{Page=Home}`                            | `/`                     | Dopasowuje `Page` `Home`i ustawia na .                                         |
| `{Page=Home}`                            | `/Contact`              | Dopasowuje `Page` `Contact`i ustawia na .                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | Mapy do `Products` kontrolera i `List` akcji.                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | Mapy do `Products` kontrolera `Details` `id` i akcji ( ustawiona na 123). |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | Mapy do `Home` kontrolera `Index` `id` i metody ( jest ignorowana).        |

Korzystanie z szablonu jest zazwyczaj najprostszym podejściem do routingu. Ograniczenia i wartości domyślne można również określić poza szablonem trasy.

> [!TIP]
> Włącz [rejestrowanie,](xref:fundamentals/logging/index) aby zobaczyć, jak wbudowane implementacje routingu, takie jak <xref:Microsoft.AspNetCore.Routing.Route>, dopasowywanie żądań.

## <a name="reserved-routing-names"></a>Nazwy marszruty zarezerwowane

Następujące słowa kluczowe są zastrzeżonymi nazwami i nie mogą być używane jako nazwy lub parametry trasy:

* `action`
* `area`
* `controller`
* `handler`
* `page`

## <a name="route-constraint-reference"></a>Odwołanie do ograniczenia trasy

Ograniczenia trasy są wykonywane, gdy dopasowanie wystąpiło z przychodzącym adresem URL, a ścieżka adresu URL jest tokenizowana na wartości trasy. Ograniczenia trasy zazwyczaj sprawdzają wartość trasy skojarzoną za pośrednictwem szablonu trasy i podejmują decyzję tak/nie o tym, czy wartość jest dopuszczalna. Niektóre ograniczenia trasy używają danych poza wartością trasy, aby rozważyć, czy żądanie może być kierowane. Na przykład <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> można zaakceptować lub odrzucić żądanie na podstawie jego zlecenia HTTP. Ograniczenia są używane w żądaniach routingu i generowaniu łączy.

> [!WARNING]
> Nie używaj ograniczeń do **sprawdzania poprawności danych wejściowych**. Jeśli ograniczenia są używane do **sprawdzania poprawności danych wejściowych,** nieprawidłowe wyniki wejściowe w *404 - Nie znaleziono* odpowiedzi zamiast *400 - Złe żądanie* z odpowiednim komunikatem o błędzie. Ograniczenia trasy są używane do **rozróżniania** podobnych tras, a nie do sprawdzania poprawności danych wejściowych dla określonej trasy.

W poniższej tabeli przedstawiono przykładowe ograniczenia trasy i ich oczekiwane zachowanie.

| ograniczenie | Przykład | Przykładowe dopasowania | Uwagi |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | `123456789`, `-123456789` | Dopasowuje dowolną całkowitą. |
| `bool` | `{active:bool}` | `true`, `FALSE` | Dopasowuje lub "false.Matches `true` or 'false. Bez uwzględniania wielkości liter. |
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | Pasuje do `DateTime` prawidłowej wartości w kulturze niezmiennej. Zobacz poprzednie ostrzeżenie.|
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Pasuje do `decimal` prawidłowej wartości w kulturze niezmiennej. Zobacz poprzednie ostrzeżenie.|
| `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | Pasuje do `double` prawidłowej wartości w kulturze niezmiennej. Zobacz poprzednie ostrzeżenie.|
| `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | Pasuje do `float` prawidłowej wartości w kulturze niezmiennej. Zobacz poprzednie ostrzeżenie.|
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Pasuje do `Guid` prawidłowej wartości. |
| `long` | `{ticks:long}` | `123456789`, `-123456789` | Pasuje do `long` prawidłowej wartości. |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | Ciąg musi mieć co najmniej 4 znaki. |
| `maxlength(value)` | `{filename:maxlength(8)}` | `MyFile` | Ciąg ma maksymalnie 8 znaków. |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | Ciąg musi mieć dokładnie 12 znaków. |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | Ciąg musi mieć co najmniej 8 znaków i maksymalnie 16 znaków. |
| `min(value)` | `{age:min(18)}` | `19` | Wartość całkowita musi wynosić co najmniej 18. |
| `max(value)` | `{age:max(120)}` | `91` | Wartość całkowita maksymalnie 120. |
| `range(min,max)` | `{age:range(18,120)}` | `91` | Wartość całkowita musi wynosić co najmniej 18 i maksymalnie 120. |
| `alpha` | `{name:alpha}` | `Rick` | Ciąg musi składać się z `a` - `z`jednego lub więcej znaków alfabetycznych .  Bez uwzględniania wielkości liter. |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | Ciąg musi być zgodny z wyrażeniem regularnym. Zobacz wskazówki dotyczące definiowania wyrażenia regularnego. |
| `required` | `{name:required}` | `Rick` | Służy do wymuszania, że wartość nieparametryczny jest obecny podczas generowania adresu URL. |

Wiele ograniczeń rozdzielanych dwukropkiem można zastosować do pojedynczego parametru. Na przykład następujące ograniczenie ogranicza parametr do wartości całkowitej 1 lub większej:

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> Ograniczenia trasy, które weryfikują adres URL i są `int` `DateTime`konwertowane na typ CLR (na przykład lub ) zawsze używają kultury niezmiennej. Te ograniczenia zakładają, że adres URL nie jest zlokalizowany. Ograniczenia trasy dostarczone przez strukturę nie modyfikują wartości przechowywanych w wartościach marszruty. Wszystkie wartości trasy analizowane z adresu URL są przechowywane jako ciągi. Na przykład `float` ograniczenie próbuje przekonwertować wartość trasy na float, ale przekonwertowana wartość jest używana tylko do sprawdzenia, czy można ją przekonwertować na float.

## <a name="regular-expressions"></a>Wyrażenia regularne

Struktura ASP.NET Core dodaje `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` do konstruktora wyrażeń regularnych. Opis <xref:System.Text.RegularExpressions.RegexOptions> tych członków można znaleźć w części.

Wyrażenia regularne używają ograniczników i tokenów podobnych do tych używanych przez routing i język C#. Tokeny wyrażenia regularnego muszą zostać zmienione. Aby użyć wyrażenia `^\d{3}-\d{2}-\d{4}$` regularnego w routingu:

* Wyrażenie musi mieć pojedyncze `\` znaki ukośnika odwrotnego `\\` podane w ciągu jako podwójne znaki ukośnika odwrotnego w kodzie źródłowym.
* Wyrażenie regularne musi `\\` nas w `\` celu ucieczki znak ucieczki ciągu.
* Wyrażenie regularne nie wymaga `\\` użycia [dosłownie literałów ciągów](/dotnet/csharp/language-reference/keywords/string).

Aby uniknąć znaków ogranicznika `}` `[`parametru marszruty `{`, `{{` `}`, `[[` `]`, podwoić znaki w wyrażeniu , , , `]]`. W poniższej tabeli przedstawiono wyrażenie regularne i wersję odstępów ekscesowych:

| Wyrażenie regularne    | Wysunęło się wyrażenie regularne     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

Wyrażenia regularne używane w routingu często `^` zaczynają się od znaku szycie i pasują do pozycji początkowej ciągu. Wyrażenia często kończą się znakiem `$` znaku dolara i odpowiadają końcu ciągu. Znaki `^` `$` i upewnij się, że wyrażenie regularne odpowiada wartości całego parametru trasy. Bez `^` znaków `$` i wyrażenie regularne odpowiada wszystkie podciągi w ciągu, który jest często niepożądane. W poniższej tabeli przedstawiono przykłady i wyjaśniono, dlaczego są zgodne lub nie dopasowywać.

| Wyrażenie   | Ciąg    | Dopasowanie | Komentarz               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | hello     | Tak   | Dopasowania podciągów     |
| `[a-z]{2}`   | 123abc456 | Tak   | Dopasowania podciągów     |
| `[a-z]{2}`   | Mz        | Tak   | Dopasowuje wyrażenie    |
| `[a-z]{2}`   | MZ        | Tak   | Nie z uwzględnieniem wielkości liter    |
| `^[a-z]{2}$` | hello     | Nie    | Zobacz `^` `$` i powyżej |
| `^[a-z]{2}$` | 123abc456 | Nie    | Zobacz `^` `$` i powyżej |

Aby uzyskać więcej informacji na temat składni wyrażeń regularnych, zobacz [.NET Framework Wyrażenia regularne](/dotnet/standard/base-types/regular-expression-language-quick-reference).

Aby ograniczyć parametr do znanego zestawu możliwych wartości, należy użyć wyrażenia regularnego. Na przykład `{action:regex(^(list|get|create)$)}` tylko `action` dopasowuje `list` `get`wartość `create`trasy do , lub . Jeśli przekazywane do słownika ograniczeń, `^(list|get|create)$` ciąg jest równoważne. Ograniczenia przekazywane w słowniku ograniczeń (nie wbudowane w szablonie), które nie pasują do jednego ze znanych ograniczeń, są również traktowane jako wyrażenia regularne.

## <a name="custom-route-constraints"></a>Niestandardowe ograniczenia trasy

Oprócz wbudowanych ograniczeń trasy ograniczenia trasy można tworzyć przez implementowanie <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> interfejsu. Interfejs <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> zawiera jedną metodę, która zwraca, `Match` `true` jeśli `false` ograniczenie jest spełnione i w inny sposób.

Aby użyć <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>niestandardowego, typ ograniczenia trasy musi być <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> zarejestrowany w kontenerze usługi aplikacji. A <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> to słownik, który mapuje <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> klucze ograniczenia trasy do implementacji, które sprawdzają te ograniczenia. Aplikacja może <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> być aktualizowana `Startup.ConfigureServices` w ramach [usługi. Wywołanie AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) lub <xref:Microsoft.AspNetCore.Routing.RouteOptions> konfiguracja `services.Configure<RouteOptions>`bezpośrednio z programem . Przykład:

```csharp
services.AddRouting(options =>
{
    options.ConstraintMap.Add("customName", typeof(MyCustomConstraint));
});
```

Ograniczenie można następnie zastosować do tras w zwykły sposób, przy użyciu nazwy określonej podczas rejestrowania typu ograniczenia. Przykład:

```csharp
[HttpGet("{id:customName}")]
public ActionResult<string> Get(string id)
```

## <a name="parameter-transformer-reference"></a>Odwołanie do transformatora parametrów

Transformatory parametrów:

* Wykonywanie podczas generowania łącza <xref:Microsoft.AspNetCore.Routing.Route>dla pliku .
* Zaimplementuj `Microsoft.AspNetCore.Routing.IOutboundParameterTransformer`plik .
* Są konfigurowane przy użyciu <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>programu .
* Należy wziąć wartość trasy parametru i przekształcić go w nową wartość ciągu.
* Wynik przy użyciu wartości przekształcone w wygenerowanym łączu.

`slugify` Na przykład niestandardowy transformator parametrów we wzorcu `blog\{article:slugify}` trasy z `Url.Action(new { article = "MyTestArticle" })` generuje `blog\my-test-article`.

Aby użyć transformatora parametrów we wzorcu <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> trasy, skonfiguruj go najpierw za pomocą: `Startup.ConfigureServices`

```csharp
services.AddRouting(options =>
{
    // Replace the type and the name used to refer to it with your own
    // IOutboundParameterTransformer implementation
    options.ConstraintMap["slugify"] = typeof(SlugifyParameterTransformer);
});
```

Transformatory parametrów są używane przez platformę do przekształcania identyfikatora URI, gdzie punkt końcowy jest rozpoznawany. Na przykład ASP.NET Core MVC używa transformatorów parametrów do przekształcania `area` `controller`wartości `action`trasy `page`używanej do dopasowania , , i .

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

W poprzedniej trasie akcja `SubscriptionManagementController.GetAll` jest dopasowywała `/subscription-management/get-all`się do identyfikatora URI . Transformator parametrów nie zmienia wartości trasy używanej do generowania łącza. Na przykład `Url.Action("GetAll", "SubscriptionManagement")` wyjścia `/subscription-management/get-all`.

ASP.NET Core udostępnia konwencje interfejsu API do używania transformatorów parametrów z wygenerowanymi trasami:

* ASP.NET Core MVC ma `Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention` konwencję interfejsu API. Ta konwencja stosuje określony transformator parametrów do wszystkich tras atrybutów w aplikacji. Transformator parametrów przekształca tokeny trasy atrybutu, ponieważ są one zastępowane. Aby uzyskać więcej informacji, zobacz [Używanie transformatora parametrów w celu dostosowania zastępowania tokenów](/aspnet/core/mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement).
* Razor Pages `Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention` ma konwencję INTERFEJSU API. Ta konwencja stosuje określony transformator parametrów do wszystkich automatycznie odnalezionych stron Razor. Transformator parametrów przekształca segmenty folderów i nazw plików tras Razor Pages. Aby uzyskać więcej informacji, zobacz [Dostosowywanie tras stron za pomocą transformatora parametrów](/aspnet/core/razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes).

## <a name="url-generation-reference"></a>Odwołanie do generowania adresów URL

W poniższym przykładzie pokazano, jak wygenerować łącze <xref:Microsoft.AspNetCore.Routing.RouteCollection>do trasy, pod jakimasemie jest słownik wartości trasy i .

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

Wygenerowany <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> na końcu poprzedniej próbki `/package/create/123`to . Słownik dostarcza wartości `operation` i `id` trasy szablonu "Track Package `package/{operation}/{id}`Route", . Aby uzyskać szczegółowe informacje, zobacz przykładowy kod w sekcji [Użyj oprogramowania pośredniczącego routingu](#use-routing-middleware) lub [w przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples).

Drugim parametrem <xref:Microsoft.AspNetCore.Routing.VirtualPathContext> konstruktora jest zbiór *wartości otoczenia*. Wartości otoczenia są wygodne w użyciu, ponieważ ograniczają liczbę wartości, które deweloper musi określić w kontekście żądania. Bieżące wartości trasy bieżącego żądania są traktowane jako wartości otoczenia dla generowania łączy. W `About` ASP.NET Core MVC app akcji `HomeController`, nie trzeba określić wartość trasy kontrolera, aby `Index` &mdash;połączyć się `Home` z akcji otaczającej wartość jest używany.

Wartości otoczenia, które nie pasują do parametru, są ignorowane. Wartości otoczenia są również ignorowane, gdy jawnie podana wartość zastępuje wartość otoczenia. Dopasowywanie odbywa się od lewej do prawej w adresie URL.

Wartości jawnie podane, ale nie pasują do segmentu trasy są dodawane do ciągu zapytania. W poniższej tabeli przedstawiono `{controller}/{action}/{id?}`wynik podczas korzystania z szablonu trasy .

| Wartości otoczenia                     | Wartości jawne                        | Wynik                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| sterownik = "Strona główna"                | akcja = "Informacje"                       | `/Home/About`           |
| sterownik = "Strona główna"                | controller = "Kolejność", akcja = "O" | `/Order/About`          |
| sterownik = "Home", kolor = "Czerwony" | akcja = "Informacje"                       | `/Home/About`           |
| sterownik = "Strona główna"                | akcja = "O", kolor = "Czerwony"        | `/Home/About?color=Red` |

Jeśli trasa ma wartość domyślną, która nie odpowiada parametrowi i ta wartość jest jawnie podana, musi być zgodna z wartością domyślną:

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

Generowanie łączy generuje tylko łącze dla `controller` tej `action` trasy, gdy pasujące wartości dla i są dostarczane.

## <a name="complex-segments"></a>Segmenty złożone

Złożone segmenty (na przykład) `[Route("/x{token}y")]`są przetwarzane przez dopasowanie literałów od prawej do lewej w sposób niechciwy. Zobacz [ten kod,](https://github.com/dotnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) aby uzyskać szczegółowe wyjaśnienie, jak segmenty złożone są dopasowywały. [Przykładowy kod](https://github.com/dotnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) nie jest używany przez ASP.NET Core, ale zapewnia dobre wyjaśnienie złożonych segmentów.
<!-- While that code is no longer used by ASP.NET Core for complex segment matching, it provides a good match to the current algorithm. The [current code](https://github.com/dotnet/AspNetCore/blob/91514c9af7e0f4c44029b51f05a01c6fe4c96e4c/src/Http/Routing/src/Matching/DfaMatcherBuilder.cs#L227-L244) is too abstracted from matching to be useful for understanding complex segment matching.
-->

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Routing jest odpowiedzialny za mapowanie identyfikatorów URI żądań do obsługi tras i wysyłania żądań przychodzących. Trasy są definiowane w aplikacji i konfigurowane po uruchomieniu aplikacji. Trasa może opcjonalnie wyodrębnić wartości z adresu URL zawartego w żądaniu, a te wartości mogą być następnie używane do przetwarzania żądań. Korzystając ze skonfigurowanych tras z aplikacji, routing jest w stanie wygenerować adresy URL, które mapują do obsługi trasy.

Aby użyć najnowszych scenariuszy routingu w ASP.NET Core 2.1, określ [wersję](xref:mvc/compatibility-version) zgodności `Startup.ConfigureServices`z rejestracją usług MVC w:

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

> [!IMPORTANT]
> Ten dokument obejmuje routing ASP.NET niskiego poziomu. Aby uzyskać informacje na temat ASP.NET routingu Core MVC, zobacz <xref:mvc/controllers/routing>. Aby uzyskać informacje na temat konwencji routingu na stronach Razor Pages, zobacz <xref:razor-pages/razor-pages-conventions>.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="routing-basics"></a>Podstawowe informacje o routingu

Większość aplikacji powinna wybrać podstawowy i opisowy schemat routingu, aby adresy URL były czytelne i znaczące. Domyślna trasa `{controller=Home}/{action=Index}/{id?}`konwencjonalna:

* Obsługuje podstawowy i opisowy schemat routingu.
* Jest użytecznym punktem wyjścia dla aplikacji opartych na interfejsie użytkownika.

Deweloperzy często dodają dodatkowe trasy terse do obszarów o dużym natężeniu ruchu aplikacji w sytuacjach specjalistycznych (na przykład blog i punkty końcowe e-commerce) przy użyciu [routingu atrybutów](xref:mvc/controllers/routing#attribute-routing) lub dedykowanych tras konwencjonalnych.

Interfejsy API sieci Web powinny używać routingu atrybutów do modelowania funkcji aplikacji jako zestawu zasobów, w których operacje są reprezentowane przez zlecenia HTTP. Oznacza to, że wiele operacji (na przykład GET, POST) na tym samym zasobie logicznym będzie używać tego samego adresu URL. Routing atrybutów zapewnia poziom kontroli, który jest potrzebny do starannego projektowania układu publicznego punktu końcowego interfejsu API.

Aplikacje Razor Pages używają domyślnego routingu konwencjonalnego do obsługi nazwanych zasobów w folderze *Strony* aplikacji. Dostępne są dodatkowe konwencje, które umożliwiają dostosowanie zachowania routingu stron Razor. Aby uzyskać więcej informacji, zobacz <xref:razor-pages/index> i <xref:razor-pages/razor-pages-conventions>.

Obsługa generowania adresów URL umożliwia tworzenie aplikacji bez twardych adresów URL kodowania, aby połączyć aplikację ze sobą. Ta obsługa umożliwia rozpoczęcie podstawowej konfiguracji routingu i modyfikowanie tras po określeniu układu zasobów aplikacji.

Usługa Routing używa tras <xref:Microsoft.AspNetCore.Routing.IRouter> implementacji do:

* Mapowanie przychodzących żądań do *obsługi tras*.
* Wygeneruj adresy URL używane w odpowiedziach.

Domyślnie aplikacja ma jedną kolekcję tras. Po odebraniu żądania trasy w kolekcji są przetwarzane w kolejności, w jakiej istnieją w kolekcji. Struktura próbuje dopasować adres URL żądania przychodzącego do <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> trasy w kolekcji, wywołując metodę na każdej trasie w kolekcji. Odpowiedzi można użyć routingu do generowania adresów URL (na przykład przekierowania lub łącza) na podstawie informacji o trasie, a tym samym uniknąć zakodowanych adresów URL, co pomaga w utrzymaniu.

System wyznaczania tras ma następujące właściwości:

* Składnia szablonu trasy służy do definiowania tras z tokenizowanymi parametrami trasy.
* Konfiguracja punktu końcowego w stylu konwencjonalnym i atrybutu jest dozwolona.
* <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>służy do określenia, czy parametr URL zawiera prawidłową wartość dla danego ograniczenia punktu końcowego.
* Modele aplikacji, takie jak MVC/Razor Pages, rejestrują wszystkie swoje trasy, które mają przewidywalną implementację scenariuszy routingu.
* Odpowiedzi można użyć routingu do generowania adresów URL (na przykład przekierowania lub łącza) na podstawie informacji o trasie, a tym samym uniknąć zakodowanych adresów URL, co pomaga w utrzymaniu.
* Generowanie adresów URL opiera się na trasach, które obsługują dowolną rozszerzalność. <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>oferuje metody tworzenia adresów URL.
<!-- fix [middleware](xref:fundamentals/middleware/index) -->
Routing jest połączony z potoku <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> [oprogramowania pośredniczącego](xref:fundamentals/middleware/index) przez klasę. [ASP.NET Core MVC](xref:mvc/overview) dodaje routing do potoku oprogramowania pośredniczącego w ramach swojej konfiguracji i obsługuje routing w aplikacjach MVC i Razor Pages. Aby dowiedzieć się, jak używać routingu jako składnika autonomicznego, zobacz sekcję [Użyj oprogramowania pośredniczącego routingu.](#use-routing-middleware)

### <a name="url-matching"></a>Dopasowywanie adresów URL

Dopasowywanie adresów URL jest procesem, w którym routing wysyła żądanie przychodzące do *programu obsługi*. Ten proces jest oparty na danych w ścieżce adresu URL, ale można go rozszerzyć, aby uwzględnić wszystkie dane w żądaniu. Możliwość wysyłania żądań do oddzielnych programów obsługi jest kluczem do skalowania rozmiaru i złożoności aplikacji.

Przychodzące żądania <xref:Microsoft.AspNetCore.Builder.RouterMiddleware>wprowadź , <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> który wywołuje metodę na każdej trasie w sekwencji. Wystąpienie <xref:Microsoft.AspNetCore.Routing.IRouter> decyduje, czy *do obsługi* żądania, ustawiając [RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler*) do non-null <xref:Microsoft.AspNetCore.Http.RequestDelegate>. Jeśli trasa ustawia program obsługi dla żądania, przetwarzanie trasy zatrzymuje się, a program obsługi jest wywoływany do przetwarzania żądania. Jeśli nie zostanie znaleziony żaden program obsługi trasy do przetwarzania żądania, oprogramowanie pośredniczące przekazuje żądanie do następnego oprogramowania pośredniczącego w potoku żądania.

Podstawowym dane <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> wejściowe do jest [RouteContext.HttpContext](xref:Microsoft.AspNetCore.Routing.RouteContext.HttpContext*) skojarzone z bieżącym żądaniem. [RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler) i [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData*) są wyjścia ustawione po trasie jest dopasowywał.

Dopasowanie, które <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> wywołuje również ustawia właściwości [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) do odpowiednich wartości na podstawie przetwarzania żądań wykonywane do tej pory.

[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) jest słownikiem *wartości trasy* wytwarzanych z trasy. Wartości te są zwykle określane przez tokenizacji adresu URL i może służyć do akceptowania danych wejściowych użytkownika lub do podejmowania dalszych decyzji wysyłki wewnątrz aplikacji.

[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) jest workiem właściwości dodatkowych danych związanych z dopasowaną trasą. <xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*>są dostarczane do obsługi kojarzenia danych o stanie z każdej trasy, dzięki czemu aplikacja może podejmować decyzje na podstawie trasy dopasowane. Te wartości są zdefiniowane przez dewelopera i **nie** wpływają na zachowanie routingu w żaden sposób. Ponadto wartości ukryte w [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) mogą być dowolnego typu, w przeciwieństwie do [RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values), które muszą być konwertowane do i z ciągów.

[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) to lista tras, które brały udział w pomyślnym dopasowaniu żądania. Trasy mogą być zagnieżdżone wewnątrz siebie. Właściwość <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> odzwierciedla ścieżkę przez drzewo logiczne tras, które doprowadziły do dopasowania. Ogólnie rzecz biorąc pierwszy <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> element w jest zbieranie trasy i powinny być używane do generowania adresów URL. Ostatni element <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> w jest program obsługi marszruty, który pasuje.

<a name="lg"></a>

### <a name="url-generation"></a>Generowanie adresu URL

Generowanie adresów URL to proces, w którym routing może tworzyć ścieżkę adresu URL na podstawie zestawu wartości trasy. Umożliwia to logiczne oddzielenie programów obsługi trasy i adresów URL, które do nich uzyskują dostęp.

Generowanie adresu URL następuje podobny proces iteracji, ale zaczyna <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> się od kodu użytkownika lub struktury wywoływania do metody kolekcji trasy. Każda *trasa* <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> ma swoją metodę wywoływaną <xref:Microsoft.AspNetCore.Routing.VirtualPathData> w sekwencji, dopóki nie zostanie zwrócona wartość null.

Podstawowe dane wejściowe <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> to:

* [VirtualPathContext.HttpContext](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext)
* [VirtualPathContext.Wartości](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values)
* [VirtualPathContext.AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues)

Trasy używają przede wszystkim wartości <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> trasy podanych przez i zdecydować, czy jest możliwe wygenerowanie adresu URL i jakie wartości mają być uwzględnione. Są <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> to zestaw wartości trasy, które zostały utworzone z dopasowania bieżącego żądania. Z kolei <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> są to wartości trasy, które określają sposób generowania żądanego adresu URL dla bieżącej operacji. Jest <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext> to przewidziane w przypadku, gdy trasa powinna uzyskać usługi lub dodatkowe dane skojarzone z bieżącym kontekstem.

> [!TIP]
> [VirtualPathContext.Values](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values*) można potraktowania jako zestawu nadpisań dla [virtualpathcontext.AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues*). Generowanie adresów URL próbuje ponownie użyć wartości trasy z bieżącego żądania do generowania adresów URL dla łączy przy użyciu tych samych wartości trasy lub trasy.

Wyjście jest <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> . <xref:Microsoft.AspNetCore.Routing.VirtualPathData> <xref:Microsoft.AspNetCore.Routing.VirtualPathData>jest równoległym <xref:Microsoft.AspNetCore.Routing.RouteData> <xref:Microsoft.AspNetCore.Routing.VirtualPathData>zawiera <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> wyjściowy adres URL i niektóre dodatkowe właściwości, które powinny być ustawione przez trasę.

Właściwość [VirtualPathData.VirtualPath](xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath*) zawiera *ścieżkę wirtualną* wyprodukowaną przez trasę. W zależności od potrzeb może być konieczne dalsze przetwarzanie ścieżki. Jeśli chcesz renderować wygenerowany adres URL w formacie HTML, należy dołączyć ścieżkę podstawową aplikacji.

[VirtualPathData.Router](xref:Microsoft.AspNetCore.Routing.VirtualPathData.Router*) jest odwołaniem do trasy, która pomyślnie wygenerowała adres URL.

[VirtualPathData.DataTokens](xref:Microsoft.AspNetCore.Routing.VirtualPathData.DataTokens*) właściwości jest słownik dodatkowych danych związanych z marszrutą, która wygenerowała adres URL. Jest to paralela [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*).

### <a name="create-routes"></a>Tworzenie tras

Routing zapewnia <xref:Microsoft.AspNetCore.Routing.Route> klasę jako standardową implementację <xref:Microsoft.AspNetCore.Routing.IRouter>. <xref:Microsoft.AspNetCore.Routing.Route>Używa składni *szablonu trasy* do definiowania wzorców dopasowanych <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> do ścieżki adresu URL, gdy jest wywoływana. <xref:Microsoft.AspNetCore.Routing.Route>używa tego samego szablonu trasy <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> do generowania adresu URL, gdy jest wywoływana.

Większość aplikacji tworzy <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> trasy przez wywołanie lub <xref:Microsoft.AspNetCore.Routing.IRouteBuilder>jedną z podobnych metod rozszerzenia zdefiniowanych w programie . Każda z <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> metod rozszerzenia utworzyć <xref:Microsoft.AspNetCore.Routing.Route> wystąpienie i dodać go do kolekcji trasy.

<xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>nie akceptuje parametru obsługi marszruty. <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>dodaje tylko trasy obsługiwane przez <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>plik . Domyślny program `IRouter`obsługi jest , a program obsługi może nie obsługiwać żądania. Na przykład ASP.NET Core MVC jest zazwyczaj skonfigurowany jako domyślny program obsługi, który obsługuje tylko żądania, które pasują do dostępnego kontrolera i akcji. Aby dowiedzieć się więcej o <xref:mvc/controllers/routing>routingu w MVC, zobacz .

Poniższy przykład kodu jest <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> przykładem wywołania używanego przez typową definicję trasy core MVC ASP.NET:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Ten szablon pasuje do ścieżki adresu URL i wyodrębnia wartości trasy. Na przykład ścieżka `/Products/Details/17` generuje następujące wartości `{ controller = Products, action = Details, id = 17 }`trasy: .

Wartości trasy są określane przez podzielenie ścieżki adresu URL na segmenty i dopasowanie każdego segmentu do nazwy *parametru trasy* w szablonie trasy. Parametry trasy są nazwane. Parametry zdefiniowane przez załączenie nazwy `{ ... }`parametru w nawiasy klamrowe .

Poprzedni szablon może również odpowiadać `/` ścieżce `{ controller = Home, action = Index }`adresu URL i tworzyć wartości . Dzieje się `{controller}` tak, ponieważ parametry `{action}` i `id` trasy mają wartości domyślne, a parametr trasy jest opcjonalny. Znak równości (`=`), po którym następuje wartość po nazwie parametru trasy definiuje wartość domyślną dla parametru. Znak zapytania`?`( ) po nazwie parametru trasy definiuje parametr opcjonalny.

Parametry trasy z wartością domyślną *zawsze* generują wartość trasy, gdy trasa jest zgodna. Parametry opcjonalne nie generują wartości trasy, jeśli nie ma odpowiedniego segmentu ścieżki adresu URL. Zobacz sekcję [odwołania do szablonu trasy,](#route-template-reference) aby uzyskać dokładny opis scenariuszy szablonu trasy i składni.

W poniższym przykładzie definicja `{id:int}` parametru trasy definiuje ograniczenie [trasy](#route-constraint-reference) dla parametru `id` trasy:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

Ten szablon pasuje do `/Products/Details/17` ścieżki `/Products/Details/Apples`adresu URL, takiej jak, ale nie . Ograniczenia trasy <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> implementują i sprawdzają wartości trasy, aby je zweryfikować. W tym przykładzie `id` wartość trasy musi być konwertowana na całkowitą. Zobacz [route-constraint-reference, aby](#route-constraint-reference) uzyskać wyjaśnienie ograniczeń trasy dostarczonych przez strukturę.

Dodatkowe przeciążenia <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> wartości `constraints`akceptowania dla , `dataTokens`i `defaults`. Typowym zastosowaniem tych parametrów jest przekazanie anonimowo wpisanego obiektu, w którym nazwy właściwości typu anonimowego odpowiadają nazwom parametrów trasy.

Poniższe <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> przykłady tworzą równoważne trasy:

```csharp
routes.MapRoute(
    name: "default_route",
    template: "{controller}/{action}/{id?}",
    defaults: new { controller = "Home", action = "Index" });

routes.MapRoute(
    name: "default_route",
    template: "{controller=Home}/{action=Index}/{id?}");
```

> [!TIP]
> Składnia wbudowana do definiowania ograniczeń i wartości domyślnych może być wygodna w przypadku prostych tras. Istnieją jednak scenariusze, takie jak tokeny danych, które nie są obsługiwane przez składnię wbudowaną.

W poniższym przykładzie przedstawiono kilka dodatkowych scenariuszy:

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{*article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

Poprzedni szablon pasuje do ścieżki `/Blog/All-About-Routing/Introduction` adresu URL, `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`takiej jak i wyodrębnia wartości . Domyślne wartości `controller` trasy `action` dla i są tworzone przez trasę, nawet jeśli w szablonie nie ma odpowiednich parametrów trasy. Wartości domyślne można określić w szablonie trasy. Parametr `article` trasy jest definiowany jako *catch-all* przez pojawienie`*`się gwiazdki ( ) przed nazwą parametru trasy. Parametry trasy catch-all przechwytują pozostałą część ścieżki adresu URL i mogą również odpowiadać pustejasce.

W poniższym przykładzie dodano ograniczenia trasy i tokeny danych:

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

Poprzedni szablon pasuje do ścieżki `/en-US/Products/5` adresu URL, `{ controller = Products, action = Details, id = 5 }` takiej jak `{ locale = en-US }`i wyodrębnia wartości i tokeny danych.

![Lokalne tokeny systemu Windows](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a>Generowanie adresu URL klasy trasy

Klasa <xref:Microsoft.AspNetCore.Routing.Route> może również wykonywać generowanie adresów URL, łącząc zestaw wartości trasy z szablonem trasy. Jest to logicznie odwrotny proces dopasowywania ścieżki adresu URL.

> [!TIP]
> Aby lepiej zrozumieć generowanie adresów URL, wyobraź sobie, jaki adres URL chcesz wygenerować, a następnie zastanów się, jak szablon trasy będzie zgodny z tym adresem URL. Jakie wartości powstałyby? Jest to odpowiednik przybliżone, <xref:Microsoft.AspNetCore.Routing.Route> jak działa generowanie adresów URL w klasie.

W poniższym przykładzie użyto ogólnej trasy domyślnej ASP.NET Core MVC:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Z wartościami `{ controller = Products, action = List }`trasy `/Products/List` generowany jest adres URL. Wartości trasy są zastępowane odpowiednimi parametrami trasy w celu utworzenia ścieżki adresu URL. Ponieważ `id` jest opcjonalnym parametrem trasy, adres URL `id`jest pomyślnie generowany bez wartości dla .

Z wartościami `{ controller = Home, action = Index }`trasy `/` generowany jest adres URL. Podane wartości trasy są zgodne z wartościami domyślnymi, a segmenty odpowiadające wartościom domyślnym są bezpiecznie pomijane.

Oba adresy URL wygenerowane w obie`/Home/Index` `/`strony z następującą definicją trasy ( i ) generują te same wartości trasy, które zostały użyte do wygenerowania adresu URL.

> [!NOTE]
> Aplikacja używająca ASP.NET Core MVC <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> powinna używać do generowania adresów URL zamiast wywoływania bezpośrednio do routingu.

Aby uzyskać więcej informacji na temat generowania adresów URL, zobacz sekcję [odwołanie do generowania adresów URL.](#url-generation-reference)

## <a name="use-routing-middleware"></a>Korzystanie z oprogramowania pośredniczącego routingu

Odwołanie się do [metapakietu Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) w pliku projektu aplikacji.

Dodaj routing do kontenera usługi w: `Startup.ConfigureServices`

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

Trasy muszą być skonfigurowane w metodzie. `Startup.Configure` Przykładowa aplikacja używa następujących interfejsów API:

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>&ndash; Pasuje tylko do żądań HTTP GET.
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

W poniższej tabeli przedstawiono odpowiedzi z podanymi identyfikatorami URI.

| Identyfikator URI                    | Odpowiedź                                          |
| ---------------------- | ------------------------------------------------- |
| `/package/create/3`    | Cześć! Wartości trasy: [operacja, tworzenie], [id, 3] |
| `/package/track/-3`    | Cześć! Wartości trasy: [operacja, ścieżka], [id, -3] |
| `/package/track/-3/`   | Cześć! Wartości trasy: [operacja, ścieżka], [id, -3] |
| `/package/track/`      | Żądanie przechodzi, bez dopasowania.              |
| `GET /hello/Joe`       | Cześć, Joe!                                          |
| `POST /hello/Joe`      | Żądanie przechodzi, pasuje tylko do protokołu HTTP GET. |
| `GET /hello/Joe/Smith` | Żądanie przechodzi, bez dopasowania.              |

Jeśli konfigurujesz jedną trasę, <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*> wywołanie `IRouter` przekazywania w wystąpieniu. Nie musisz używać <xref:Microsoft.AspNetCore.Routing.RouteBuilder>.

Struktura zawiera zestaw metod rozszerzenia do tworzenia<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>tras ( ):

* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareVerb*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>

Niektóre z wymienionych metod, takie jak <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>, wymagają . <xref:Microsoft.AspNetCore.Http.RequestDelegate> Jest <xref:Microsoft.AspNetCore.Http.RequestDelegate> używany jako *program obsługi trasy,* gdy trasa jest zgodna. Inne metody w tej rodzinie umożliwiają konfigurowanie potoku oprogramowania pośredniczącego do użycia jako program obsługi trasy. Jeśli `Map*` metoda nie akceptuje programu obsługi, <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>na przykład <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>, używa .

Metody `Map[Verb]` używają ograniczeń, aby ograniczyć trasę do zlecenia HTTP w nazwie metody. Na przykład <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> zobacz <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>i .

## <a name="route-template-reference"></a>Odwołanie do szablonu trasy

Tokeny w nawiasach`{ ... }`klamrowych ( ) definiują *parametry trasy,* które są powiązane, jeśli trasa jest dopasowywała. Można zdefiniować więcej niż jeden parametr trasy w segmencie trasy, ale muszą one być oddzielone wartością literału. Na przykład `{controller=Home}{action=Index}` nie jest prawidłową trasą, ponieważ nie `{controller}` ma `{action}`wartości literału między i . Te parametry trasy muszą mieć nazwę i mogą mieć określone dodatkowe atrybuty.

Tekst dosłowny inny niż `{id}`parametry trasy (na `/` przykład) i separator ścieżki muszą być zgodne z tekstem w adresie URL. Dopasowanie tekstu jest niewrażliwe na rozmiary i opiera się na zdekodowanym reprezentacji ścieżki adresów URL. `{` Aby dopasować dosłowny ogranicznik parametru trasy ( lub `}``{{` ), `}}`wyjdź z ogranicznika, powtarzając znak ( lub ).

Wzorce adresów URL, które próbują przechwycić nazwę pliku z opcjonalnym rozszerzeniem pliku, mają dodatkowe uwagi. Rozważmy na przykład `files/{filename}.{ext?}`szablon . Gdy wartości `filename` dla `ext` obu i istnieją, obie wartości są wypełniane. Jeśli w adresie URL `filename` istnieje tylko wartość, trasa jest`.`zgodna, ponieważ okres końcowy ( ) jest opcjonalny. Następujące adresy URL są zgodne z tą trasą:

* `/files/myFile.txt`
* `/files/myFile`

Gwiazdkę (`*`) można użyć jako prefiksu parametru trasy do powiązania z resztą identyfikatora URI. Jest to tak zwany parametr *catch-all.* Na przykład `blog/{*slug}` dopasowuje dowolny identyfikator `/blog` URI, który rozpoczyna się od `slug` i ma dowolną wartość po nim, która jest przypisana do wartości trasy. Catch-all parametry mogą również odpowiadać pusty ciąg.

Parametr catch-all wycina odpowiednie znaki, gdy trasa jest używana do`/`generowania adresu URL, w tym separatora ścieżki ( ) znaków. Na przykład trasa `foo/{*path}` z `{ path = "my/path" }` wartościami `foo/my%2Fpath`trasy generuje . Zwróć uwagę na ukucie do przodu.

Parametry trasy mogą mieć *wartości domyślne* wyznaczone przez określenie wartości domyślnej po`=`nazwie parametru oddzielonej znakiem równości ( ). Na przykład `{controller=Home}` definiuje `Home` jako wartość `controller`domyślną dla . Wartość domyślna jest używana, jeśli w adresie URL parametru nie ma żadnej wartości. Parametry trasy są opcjonalne, dołączając`?`znak zapytania ( ) na końcu `id?`nazwy parametru, jak w . Różnica między wartościami opcjonalnymi a domyślnymi parametrami trasy polega na&mdash;tym, że parametr trasy z wartością domyślną zawsze tworzy wartość, którą parametr opcjonalny ma wartość tylko wtedy, gdy wartość jest dostarczana przez adres URL żądania.

Parametry trasy mogą mieć ograniczenia, które muszą odpowiadać wartości trasy powiązanej z adresem URL. Dodanie dwukropka (`:`) i nazwy ograniczenia po nazwie parametru trasy określa ograniczenie *wbudowane* dla parametru trasy. Jeśli ograniczenie wymaga argumentów, są one ujęte`(...)`w nawiasy ( ) po nazwie ograniczenia. Można określić wiele ograniczeń wbudowanych, dołączając`:`inny dwukropek ( ) i nazwę ograniczenia.

Nazwa ograniczenia i argumenty są <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> przekazywane do usługi, aby utworzyć wystąpienie do użycia w przetwarzaniu adresów <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> URL. Na przykład szablon `blog/{article:minlength(10)}` trasy określa `minlength` ograniczenie z `10`argumentem . Aby uzyskać więcej informacji na temat ograniczeń trasy i listy ograniczeń przewidzianych przez strukturę, zobacz [sekcja Odwołanie do ograniczenia trasy.](#route-constraint-reference)

W poniższej tabeli przedstawiono przykładowe szablony tras i ich zachowanie.

| Szablon trasy                           | Przykładowy pasujący identyfikator URI    | Identyfikator URI żądania&hellip;                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | Pasuje tylko do `/hello`pojedynczej ścieżki .                                     |
| `{Page=Home}`                            | `/`                     | Dopasowuje `Page` `Home`i ustawia na .                                         |
| `{Page=Home}`                            | `/Contact`              | Dopasowuje `Page` `Contact`i ustawia na .                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | Mapy do `Products` kontrolera i `List` akcji.                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | Mapy do `Products` kontrolera `Details` `id` i akcji ( ustawiona na 123). |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | Mapy do `Home` kontrolera `Index` `id` i metody ( jest ignorowana).        |

Korzystanie z szablonu jest zazwyczaj najprostszym podejściem do routingu. Ograniczenia i wartości domyślne można również określić poza szablonem trasy.

> [!TIP]
> Włącz [rejestrowanie,](xref:fundamentals/logging/index) aby zobaczyć, jak wbudowane implementacje routingu, takie jak <xref:Microsoft.AspNetCore.Routing.Route>, dopasowywanie żądań.

## <a name="route-constraint-reference"></a>Odwołanie do ograniczenia trasy

Ograniczenia trasy są wykonywane, gdy dopasowanie wystąpiło z przychodzącym adresem URL, a ścieżka adresu URL jest tokenizowana na wartości trasy. Ograniczenia trasy zazwyczaj sprawdzają wartość trasy skojarzoną za pośrednictwem szablonu trasy i podejmują decyzję tak/nie o tym, czy wartość jest dopuszczalna. Niektóre ograniczenia trasy używają danych poza wartością trasy, aby rozważyć, czy żądanie może być kierowane. Na przykład <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> można zaakceptować lub odrzucić żądanie na podstawie jego zlecenia HTTP. Ograniczenia są używane w żądaniach routingu i generowaniu łączy.

> [!WARNING]
> Nie używaj ograniczeń do **sprawdzania poprawności danych wejściowych**. Jeśli ograniczenia są używane do **sprawdzania poprawności danych wejściowych,** nieprawidłowe wyniki wejściowe w *404 - Nie znaleziono* odpowiedzi zamiast *400 - Złe żądanie* z odpowiednim komunikatem o błędzie. Ograniczenia trasy są używane do **rozróżniania** podobnych tras, a nie do sprawdzania poprawności danych wejściowych dla określonej trasy.

W poniższej tabeli przedstawiono przykładowe ograniczenia trasy i ich oczekiwane zachowanie.

| ograniczenie | Przykład | Przykładowe dopasowania | Uwagi |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | `123456789`, `-123456789` | Dopasowuje dowolną całkowitą |
| `bool` | `{active:bool}` | `true`, `FALSE` | Dopasowania `true` `false` lub (niewrażliwe na wielkości liter) |
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | Pasuje do `DateTime` prawidłowej wartości w kulturze niezmiennej. Zobacz poprzednie ostrzeżenie.|
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Pasuje do `decimal` prawidłowej wartości w kulturze niezmiennej. Zobacz poprzednie ostrzeżenie.|
| `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | Pasuje do `double` prawidłowej wartości w kulturze niezmiennej. Zobacz poprzednie ostrzeżenie.|
| `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | Pasuje do `float` prawidłowej wartości w kulturze niezmiennej. Zobacz poprzednie ostrzeżenie.|
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Dopasowuje `Guid` prawidłową wartość |
| `long` | `{ticks:long}` | `123456789`, `-123456789` | Dopasowuje `long` prawidłową wartość |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | Ciąg musi zawierać co najmniej 4 znaki |
| `maxlength(value)` | `{filename:maxlength(8)}` | `Richard` | Ciąg nie może zawierać więcej niż 8 znaków |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | Ciąg musi mieć dokładnie 12 znaków |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | Ciąg musi mieć co najmniej 8 znaków i nie więcej niż 16 znaków |
| `min(value)` | `{age:min(18)}` | `19` | Wartość całkowita musi wynosić co najmniej 18 |
| `max(value)` | `{age:max(120)}` | `91` | Wartość całkowita nie może być większa niż 120 |
| `range(min,max)` | `{age:range(18,120)}` | `91` | Wartość całkowita musi wynosić co najmniej 18, ale nie więcej niż 120 |
| `alpha` | `{name:alpha}` | `Rick` | Ciąg musi składać się z jednego`a`-`z`lub więcej znaków alfabetycznych ( , bez uwzględniania wielkości liter) |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | Ciąg musi być zgodny z wyrażeniem regularnym (zobacz wskazówki dotyczące definiowania wyrażenia regularnego) |
| `required` | `{name:required}` | `Rick` | Służy do wymuszania obecności wartości nieparametryjnej podczas generowania adresu URL |

Wiele ograniczeń rozdzielanych dwukropkiem można zastosować do pojedynczego parametru. Na przykład następujące ograniczenie ogranicza parametr do wartości całkowitej 1 lub większej:

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> Ograniczenia trasy, które weryfikują adres URL i są `int` `DateTime`konwertowane na typ CLR (na przykład lub ) zawsze używają kultury niezmiennej. Te ograniczenia zakładają, że adres URL nie jest zlokalizowany. Ograniczenia trasy dostarczone przez strukturę nie modyfikują wartości przechowywanych w wartościach marszruty. Wszystkie wartości trasy analizowane z adresu URL są przechowywane jako ciągi. Na przykład `float` ograniczenie próbuje przekonwertować wartość trasy na float, ale przekonwertowana wartość jest używana tylko do sprawdzenia, czy można ją przekonwertować na float.

## <a name="regular-expressions"></a>Wyrażenia regularne

Struktura ASP.NET Core dodaje `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` do konstruktora wyrażeń regularnych. Opis <xref:System.Text.RegularExpressions.RegexOptions> tych członków można znaleźć w części.

Wyrażenia regularne używają ograniczników i tokenów podobnych do tych używanych przez routing i język C#. Tokeny wyrażenia regularnego muszą zostać zmienione. Aby użyć wyrażenia `^\d{3}-\d{2}-\d{4}$` regularnego w routingu, `\` wyrażenie musi mieć (pojedyncze ukośnik odwrotny) znaki podane w ciągu jako `\\` `\` (podwójne ukośnik wsteczny) znaków w pliku źródłowym C# w celu ucieczki znak ucieczki ciągu (chyba że przy użyciu [dosłownie literałów ciągów](/dotnet/csharp/language-reference/keywords/string)). Aby uniknąć znaków ogranicznika`{` `}`parametru `]`marszruty ( ,`{{` `}`, `[[` `[` `]]`, ), podwoić znaki w wyrażeniu ( , , , ). W poniższej tabeli przedstawiono wyrażenie regularne i wersję ze strony osób zmienionych.

| Wyrażenie regularne    | Wysunęło się wyrażenie regularne     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

Wyrażenia regularne używane w routingu często`^`zaczynają się od znaku cieszy ( ) i pasują do pozycji początkowej ciągu. Wyrażenia często kończą się znakiem`$`dolara ( ) znakiem i dopasowaniem końca ciągu. Znaki `^` `$` i upewnij się, że wyrażenie regularne odpowiada wartości całego parametru trasy. Bez `^` znaków `$` i wyrażenie regularne odpowiada wszystkie podciągi w ciągu, który jest często niepożądane. W poniższej tabeli przedstawiono przykłady i wyjaśniono, dlaczego są zgodne lub nie dopasowywać.

| Wyrażenie   | Ciąg    | Dopasowanie | Komentarz               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | hello     | Tak   | Dopasowania podciągów     |
| `[a-z]{2}`   | 123abc456 | Tak   | Dopasowania podciągów     |
| `[a-z]{2}`   | Mz        | Tak   | Dopasowuje wyrażenie    |
| `[a-z]{2}`   | MZ        | Tak   | Nie z uwzględnieniem wielkości liter    |
| `^[a-z]{2}$` | hello     | Nie    | Zobacz `^` `$` i powyżej |
| `^[a-z]{2}$` | 123abc456 | Nie    | Zobacz `^` `$` i powyżej |

Aby uzyskać więcej informacji na temat składni wyrażeń regularnych, zobacz [.NET Framework Wyrażenia regularne](/dotnet/standard/base-types/regular-expression-language-quick-reference).

Aby ograniczyć parametr do znanego zestawu możliwych wartości, należy użyć wyrażenia regularnego. Na przykład `{action:regex(^(list|get|create)$)}` tylko `action` dopasowuje `list` `get`wartość `create`trasy do , lub . Jeśli przekazywane do słownika ograniczeń, `^(list|get|create)$` ciąg jest równoważne. Ograniczenia przekazywane w słowniku ograniczeń (nie wbudowane w szablonie), które nie pasują do jednego ze znanych ograniczeń, są również traktowane jako wyrażenia regularne.

## <a name="custom-route-constraints"></a>Niestandardowe ograniczenia trasy

Oprócz wbudowanych ograniczeń trasy ograniczenia trasy można tworzyć przez implementowanie <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> interfejsu. Interfejs <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> zawiera jedną metodę, która zwraca, `Match` `true` jeśli `false` ograniczenie jest spełnione i w inny sposób.

Aby użyć <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>niestandardowego, typ ograniczenia trasy musi być <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> zarejestrowany w kontenerze usługi aplikacji. A <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> to słownik, który mapuje <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> klucze ograniczenia trasy do implementacji, które sprawdzają te ograniczenia. Aplikacja może <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> być aktualizowana `Startup.ConfigureServices` w ramach [usługi. Wywołanie AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) lub <xref:Microsoft.AspNetCore.Routing.RouteOptions> konfiguracja `services.Configure<RouteOptions>`bezpośrednio z programem . Przykład:

```csharp
services.AddRouting(options =>
{
    options.ConstraintMap.Add("customName", typeof(MyCustomConstraint));
});
```

Ograniczenie można następnie zastosować do tras w zwykły sposób, przy użyciu nazwy określonej podczas rejestrowania typu ograniczenia. Przykład:

```csharp
[HttpGet("{id:customName}")]
public ActionResult<string> Get(string id)
```

## <a name="url-generation-reference"></a>Odwołanie do generowania adresów URL

W poniższym przykładzie pokazano, jak wygenerować łącze <xref:Microsoft.AspNetCore.Routing.RouteCollection>do trasy, pod jakimasemie jest słownik wartości trasy i .

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

Wygenerowany <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> na końcu poprzedniej próbki `/package/create/123`to . Słownik dostarcza wartości `operation` i `id` trasy szablonu "Track Package `package/{operation}/{id}`Route", . Aby uzyskać szczegółowe informacje, zobacz przykładowy kod w sekcji [Użyj oprogramowania pośredniczącego routingu](#use-routing-middleware) lub [w przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples).

Drugim parametrem <xref:Microsoft.AspNetCore.Routing.VirtualPathContext> konstruktora jest zbiór *wartości otoczenia*. Wartości otoczenia są wygodne w użyciu, ponieważ ograniczają liczbę wartości, które deweloper musi określić w kontekście żądania. Bieżące wartości trasy bieżącego żądania są traktowane jako wartości otoczenia dla generowania łączy. W `About` ASP.NET Core MVC app akcji `HomeController`, nie trzeba określić wartość trasy kontrolera, aby `Index` &mdash;połączyć się `Home` z akcji otaczającej wartość jest używany.

Wartości otoczenia, które nie pasują do parametru, są ignorowane. Wartości otoczenia są również ignorowane, gdy jawnie podana wartość zastępuje wartość otoczenia. Dopasowywanie odbywa się od lewej do prawej w adresie URL.

Wartości jawnie podane, ale nie pasują do segmentu trasy są dodawane do ciągu zapytania. W poniższej tabeli przedstawiono `{controller}/{action}/{id?}`wynik podczas korzystania z szablonu trasy .

| Wartości otoczenia                     | Wartości jawne                        | Wynik                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| sterownik = "Strona główna"                | akcja = "Informacje"                       | `/Home/About`           |
| sterownik = "Strona główna"                | controller = "Kolejność", akcja = "O" | `/Order/About`          |
| sterownik = "Home", kolor = "Czerwony" | akcja = "Informacje"                       | `/Home/About`           |
| sterownik = "Strona główna"                | akcja = "O", kolor = "Czerwony"        | `/Home/About?color=Red` |

Jeśli trasa ma wartość domyślną, która nie odpowiada parametrowi i ta wartość jest jawnie podana, musi być zgodna z wartością domyślną:

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

Generowanie łączy generuje tylko łącze dla `controller` tej `action` trasy, gdy pasujące wartości dla i są dostarczane.

## <a name="complex-segments"></a>Segmenty złożone

Złożone segmenty (na przykład) `[Route("/x{token}y")]`są przetwarzane przez dopasowanie literałów od prawej do lewej w sposób niechciwy. Zobacz [ten kod,](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) aby uzyskać szczegółowe wyjaśnienie, jak segmenty złożone są dopasowywały. [Przykładowy kod](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) nie jest używany przez ASP.NET Core, ale zapewnia dobre wyjaśnienie złożonych segmentów.

::: moniker-end
