---
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

---
# <a name="routing-in-aspnet-core"></a>Routing w ASP.NET Core

[Ryan Nowak](https://github.com/rynowak), [Kirka Larkin](https://twitter.com/serpent5)i [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Routing jest odpowiedzialny za pasujące przychodzące żądania HTTP i wysyłając te żądania do wykonywalnych punktów końcowych aplikacji. [Punkty końcowe](#endpoint) to jednostki aplikacji wykonywalnego kodu obsługi żądania. Punkty końcowe są zdefiniowane w aplikacji i konfigurowane podczas uruchamiania aplikacji. Proces dopasowywania punktów końcowych może wyodrębnić wartości z adresu URL żądania i podać te wartości dla przetwarzania żądań. Korzystając z informacji o punkcie końcowym z aplikacji, routing może również generować adresy URL mapowane na punkty końcowe.

Aplikacje mogą konfigurować Routing przy użyciu:

- Kontrolery
- RazorPage
- SignalR
- Usługi gRPC Services
- [Oprogramowanie pośredniczące](xref:fundamentals/middleware/index) z obsługą punktu końcowego, takie jak [kontrole kondycji](xref:host-and-deploy/health-checks).
- Obiekty delegowane i wyrażenia lambda zarejestrowane z routingiem.

W tym dokumencie przedstawiono szczegóły niskiego poziomu ASP.NET Core routingu. Aby uzyskać informacje na temat konfigurowania routingu:

* W przypadku kontrolerów należy zapoznać się z tematem <xref:mvc/controllers/routing> .
* W przypadku Razor Konwencji stron, zobacz <xref:razor-pages/razor-pages-conventions> .

System routingu punktu końcowego opisany w tym dokumencie ma zastosowanie do ASP.NET Core 3,0 i nowszych. Aby uzyskać informacje dotyczące poprzedniego systemu routingu opartego na programie <xref:Microsoft.AspNetCore.Routing.IRouter> , wybierz wersję ASP.NET Core 2,1 przy użyciu jednej z następujących metod:

* Selektor wersji dla poprzedniej wersji.
* Wybierz pozycję [ASP.NET Core 2,1 Routing](https://docs.microsoft.com/aspnet/core/fundamentals/routing?view=aspnetcore-2.1).

[Wyświetl lub pobierz przykładowy kod](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples/3.x) ([jak pobrać](xref:index#how-to-download-a-sample))

Próbki pobierania dla tego dokumentu są włączane przez konkretną `Startup` klasę. Aby uruchomić określony przykład, zmodyfikuj *program.cs* w celu wywołania żądanej `Startup` klasy.

## <a name="routing-basics"></a>Podstawy routingu

Wszystkie szablony ASP.NET Core obejmują Routing w wygenerowanym kodzie. Routing jest zarejestrowany w potoku [pośredniczącego](xref:fundamentals/middleware/index) w programie `Startup.Configure` .

Poniższy kod przedstawia podstawowy przykład routingu:

[!code-csharp[](routing/samples/3.x/RoutingSample/Startup.cs?name=snippet&highlight=8,10)]

Routing używa pary programów pośredniczących zarejestrowanych przez program <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> i <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> :

* `UseRouting`dodaje dopasowanie trasy do potoku programu pośredniczącego. To oprogramowanie pośredniczące sprawdza zestaw punktów końcowych zdefiniowanych w aplikacji i wybiera [najlepsze dopasowanie](#urlm) na podstawie żądania.
* `UseEndpoints`dodaje wykonywanie punktu końcowego do potoku programu pośredniczącego. Powoduje uruchomienie delegata skojarzonego z wybranym punktem końcowym.

W powyższym przykładzie użyto pojedynczej *trasy do* punktu końcowego kodu przy użyciu metody [MapGet](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*) :

* Gdy żądanie HTTP `GET` zostanie wysłane do głównego adresu URL `/` :
  * Zostanie wyświetlony delegat żądania.
  * `Hello World!`jest zapisywana w odpowiedzi HTTP. Domyślnie główny adres URL `/` to `https://localhost:5001/` .
* Jeśli metoda żądania nie jest `GET` lub nie jest głównym adresem URL, nie są `/` zwracane żadne dopasowania tras i HTTP 404.

### <a name="endpoint"></a>Endpoint

<a name="endpoint"></a>

`MapGet`Metoda jest używana do definiowania **punktu końcowego**. Punkt końcowy to coś, co może być:

* Wybierany przez dopasowanie adresu URL i metody HTTP.
* Wykonywane przez uruchomienie delegata.

Punkty końcowe, które mogą być dopasowane i wykonywane przez aplikację, są skonfigurowane w programie `UseEndpoints` . Na przykład, <xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*> , <xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapPost*> i [podobne metody](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions) Połącz delegatów żądań z systemem routingu.
Do łączenia funkcji ASP.NET Core Framework z systemem routingu można używać dodatkowych metod:
- [MapRazorPages dla Razor stron](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*)
- [MapControllers dla kontrolerów](xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*)
- [MapHub \<THub> dlaSignalR](xref:Microsoft.AspNetCore.SignalR.HubRouteBuilder.MapHub*) 
- [MapGrpcService \<TService> dla gRPC](xref:grpc/aspnetcore)

W poniższym przykładzie przedstawiono Routing z bardziej zaawansowanym szablonem trasy:

[!code-csharp[](routing/samples/3.x/RoutingSample/RouteTemplateStartup.cs?name=snippet)]

Ciąg `/hello/{name:alpha}` jest **szablonem trasy**. Służy do konfigurowania sposobu dopasowywania punktu końcowego. W tym przypadku szablon pasuje do:

* Adres URL, taki jak`/hello/Ryan`
* Dowolna ścieżka URL, która rozpoczyna się od `/hello/` po którym następuje sekwencja znaków alfabetu.  `:alpha`stosuje ograniczenie trasy, które pasuje tylko do znaków alfabetu. [Ograniczenia trasy](#route-constraint-reference) zostały omówione w dalszej części tego dokumentu.

Drugi segment ścieżki URL `{name:alpha}` :

* Jest powiązany z `name` parametrem.
* Jest przechwytywany i przechowywany w usłudze [HttpRequest. RouteValues](xref:Microsoft.AspNetCore.Http.HttpRequest.RouteValues*).

System routingu punktu końcowego opisany w tym dokumencie jest nowy w ASP.NET Core 3,0. Jednak wszystkie wersje ASP.NET Core obsługują ten sam zestaw funkcji szablonu trasy i ograniczenia trasy.

W poniższym przykładzie przedstawiono Routing z [kontrolami kondycji](xref:host-and-deploy/health-checks) i autoryzacją:

[!code-csharp[](routing/samples/3.x/RoutingSample/AuthorizationStartup.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

W poprzednim przykładzie pokazano, jak:

* Oprogramowanie pośredniczące autoryzacji może być używane z routingiem.
* Punkty końcowe mogą służyć do konfigurowania zachowania autoryzacji.

<xref:Microsoft.AspNetCore.Builder.HealthCheckEndpointRouteBuilderExtensions.MapHealthChecks*>Wywołanie dodaje punkt końcowy sprawdzania kondycji. Tworzenie łańcucha <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization*> w ramach tego wywołania powoduje dołączenie zasad autoryzacji do punktu końcowego.

Wywoływanie <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> i <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization*> Dodawanie oprogramowania pośredniczącego uwierzytelniania i autoryzacji. Te oprogramowanie pośredniczące jest umieszczane między programem <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> i `UseEndpoints` tak, aby mogły:

* Zobacz, który punkt końcowy został wybrany przez `UseRouting` .
* Zastosuj zasady autoryzacji przed <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> wysłaniem do punktu końcowego.

<a name="metadata"></a>

### <a name="endpoint-metadata"></a>Metadane punktu końcowego

W poprzednim przykładzie istnieją dwa punkty końcowe, ale tylko punkt końcowy sprawdzania kondycji ma dołączone zasady autoryzacji. Jeśli żądanie jest zgodne z punktem końcowym sprawdzania kondycji, `/healthz` zostanie wykonane sprawdzanie autoryzacji. Pokazuje, że punkty końcowe mogą mieć dodatkowe dane dołączone do nich. Te dodatkowe dane są nazywane **metadanymi**punktu końcowego:

* Metadane mogą być przetwarzane przez oprogramowanie pośredniczące obsługujące Routing.
* Metadane mogą być dowolnego typu .NET.

## <a name="routing-concepts"></a>Pojęcia dotyczące routingu

System routingu jest kompilowany na podstawie potoku programu pośredniczącego przez dodanie zaawansowanej koncepcji **punktu końcowego** . Punkty końcowe reprezentują jednostki funkcji aplikacji, które różnią się od siebie pod względem routingu, autoryzacji i dowolnej liczby systemów ASP.NET Core.

<a name="endpoint"></a>

### <a name="aspnet-core-endpoint-definition"></a>Definicja punktu końcowego ASP.NET Core

Punkt końcowy ASP.NET Core:

* Plik wykonywalny: ma <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate> .
* Rozszerzalne: zawiera kolekcję [metadanych](xref:Microsoft.AspNetCore.Http.Endpoint.Metadata*) .
* Wybór: Opcjonalnie, zawiera [Informacje o routingu](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern*).
* Wyliczalne: Kolekcja punktów końcowych może być wyświetlona przez pobranie <xref:Microsoft.AspNetCore.Routing.EndpointDataSource> z elementu [di](xref:fundamentals/dependency-injection).

Poniższy kod pokazuje, jak pobrać i sprawdzić punkt końcowy pasujący do bieżącego żądania:

[!code-csharp[](routing/samples/3.x/RoutingSample/EndpointInspectorStartup.cs?name=snippet)]

Punkt końcowy, w przypadku wybrania, można pobrać z `HttpContext` . Jego właściwości można sprawdzić. Obiekty punktu końcowego są niezmienne i nie można ich modyfikować po utworzeniu. Najczęściej spotykanym typem punktu końcowego jest <xref:Microsoft.AspNetCore.Routing.RouteEndpoint> . `RouteEndpoint`zawiera informacje, które mogą być wybierane przez system routingu.

W poprzednim kodzie [aplikacja. Służy](xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*) do konfigurowania [oprogramowania pośredniczącego](xref:fundamentals/middleware/index)w wierszu.

<a name="mt"></a>

Poniższy kod pokazuje, że w zależności od tego, gdzie `app.Use` jest wywoływana w potoku, nie może istnieć punkt końcowy:

[!code-csharp[](routing/samples/3.x/RoutingSample/MiddlewareFlowStartup.cs?name=snippet)]

Ten poprzedni przykład dodaje `Console.WriteLine` instrukcje, które wyświetlają czy punkt końcowy został wybrany. Dla jasności przykład przypisuje nazwę wyświetlaną do podanego `/` punktu końcowego.

Uruchomienie tego kodu z adresem URL `/` wyświetlanymi:

```txt
1. Endpoint: (null)
2. Endpoint: Hello
3. Endpoint: Hello
```

Uruchomienie tego kodu z dowolnym innym adresem URL powoduje wyświetlenie:

```txt
1. Endpoint: (null)
2. Endpoint: (null)
4. Endpoint: (null)
```

Te dane wyjściowe pokazują, że:

* Punkt końcowy ma zawsze wartość null przed `UseRouting` wywołaniem metody.
* W przypadku znalezienia dopasowania punkt końcowy nie ma wartości null między `UseRouting` i <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> .
* `UseEndpoints`Oprogramowanie pośredniczące jest **terminalem** , gdy zostanie znalezione dopasowanie. [Oprogramowanie pośredniczące terminalu](#tm) zostało zdefiniowane w dalszej części tego dokumentu.
* Oprogramowanie pośredniczące po wykonaniu tylko wtedy, `UseEndpoints` gdy nie zostanie znalezione żadne dopasowanie.

`UseRouting`Oprogramowanie pośredniczące używa metody [setendpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.SetEndpoint*) do dołączania punktu końcowego do bieżącego kontekstu. Można zastąpić `UseRouting` oprogramowanie pośredniczące logiką niestandardową i nadal korzystać z punktów końcowych. Punkty końcowe są typu podstawowego niskiego poziomu, takiego jak oprogramowanie pośredniczące, i nie są połączone z implementacją routingu. Większość aplikacji nie musi zastąpić `UseRouting` logiki niestandardowej.

`UseEndpoints`Oprogramowanie pośredniczące jest przeznaczone do użycia wspólnie z programem `UseRouting` pośredniczącym. Logika podstawowa do wykonania punktu końcowego nie jest skomplikowana. Użyj <xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*> do pobrania punktu końcowego, a następnie Wywołaj jego <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate> Właściwość.

Poniższy kod ilustruje sposób, w jaki oprogramowanie pośredniczące może mieć wpływ na Routing lub reagować na nie:

[!code-csharp[](routing/samples/3.x/RoutingSample/IntegratedMiddlewareStartup.cs?name=snippet)]

W poprzednim przykładzie pokazano dwa ważne pojęcia:

* Oprogramowanie pośredniczące może zostać uruchomione przed `UseRouting` modyfikacją danych, na których działa Routing.
    * Zazwyczaj oprogramowanie pośredniczące, które pojawia się przed wybraniem trasy modyfikuje pewne właściwości żądania, takie jak <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*> , <xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions.UseHttpMethodOverride*> lub <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> .
* Oprogramowanie pośredniczące może działać między programami `UseRouting` i, <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> Aby przetwarzać wyniki routingu przed wykonaniem punktu końcowego.
    * Oprogramowanie pośredniczące, które działa między `UseRouting` i `UseEndpoints` :
      * Zwykle sprawdza metadane, aby zrozumieć punkty końcowe.
      * Często podejmuje decyzje dotyczące zabezpieczeń, zgodnie z oczekiwaniami `UseAuthorization` i `UseCors` .
    * Kombinacja oprogramowania pośredniczącego i metadanych umożliwia konfigurowanie zasad dla poszczególnych punktów końcowych.

Powyższy kod przedstawia przykład niestandardowego oprogramowania pośredniczącego, które obsługuje zasady dotyczące poszczególnych punktów końcowych. Oprogramowanie pośredniczące zapisuje *Dziennik inspekcji* dostępu do poufnych danych w konsoli programu. Oprogramowanie pośredniczące można skonfigurować do *inspekcji* punktu końcowego przy użyciu `AuditPolicyAttribute` metadanych. Ten przykład pokazuje wzorzec *wyboru, w* którym są poddawane inspekcji tylko punkty końcowe oznaczone jako poufne. Istnieje możliwość zdefiniowania tej logiki na odwrót, przeprowadzenia inspekcji wszystkich elementów, które nie są oznaczone jako bezpieczne, na przykład. System metadanych punktu końcowego jest elastyczny. Ta logika może być zaprojektowana w dowolny sposób, jak w przypadku użycia.

Poprzedni przykładowy kod jest przeznaczony do zademonstrowania podstawowych koncepcji punktów końcowych. **Przykład nie jest przeznaczony do użycia w środowisku produkcyjnym**. Bardziej kompletna wersja oprogramowania pośredniczącego *dziennika inspekcji* :

* Zaloguj się do pliku lub bazy danych.
* Dołącz szczegóły, takie jak użytkownik, adres IP, nazwa poufnego punktu końcowego itd.

Metadane zasad inspekcji `AuditPolicyAttribute` są zdefiniowane jako `Attribute` ułatwiające korzystanie z struktur opartych na klasach, takich jak kontrolery i SignalR . W przypadku używania *trasy do kodu*:

* Metadane są dołączone do interfejsu API programu Builder.
* Struktury oparte na klasie obejmują wszystkie atrybuty w odpowiedniej metodzie i klasie podczas tworzenia punktów końcowych.

Najlepszymi rozwiązaniami dotyczącymi typów metadanych są Definiowanie ich jako interfejsów lub atrybutów. Interfejsy i atrybuty umożliwiają ponowne użycie kodu. System metadanych jest elastyczny i nie nakłada żadnych ograniczeń.

<a name="tm"></a>

### <a name="comparing-a-terminal-middleware-and-routing"></a>Porównanie oprogramowania pośredniczącego i routingu terminalu

Poniższy przykładowy kod kontrast korzystający z oprogramowania pośredniczącego z użyciem routingu:

[!code-csharp[](routing/samples/3.x/RoutingSample/TerminalMiddlewareStartup.cs?name=snippet)]

Styl oprogramowania pośredniczącego widoczny w programie `Approach 1:` to **terminalowe oprogramowanie pośredniczące**. Jest on nazywany oprogramowanie pośredniczące terminalu, ponieważ wykonuje operację zgodną:

* Operacja dopasowywania w poprzednim przykładzie dotyczy `Path == "/"` oprogramowania pośredniczącego i `Path == "/Movie"` routingu.
* Gdy dopasowanie zakończy się pomyślnie, wykonuje pewne funkcje i zwraca zamiast wywoływania `next` oprogramowania pośredniczącego.

Jest on nazywany oprogramowanie pośredniczące terminalu, ponieważ kończy wyszukiwanie, wykonuje pewne funkcje, a następnie zwraca.

Porównanie oprogramowania pośredniczącego i routingu w terminalu:
* Obie metody umożliwiają zakończenie potoku przetwarzania:
    * Oprogramowanie pośredniczące kończy potok przez zwrócenie zamiast wywoływania `next` .
    * Punkty końcowe są zawsze terminalem.
* Oprogramowanie pośredniczące terminalu umożliwia rozmieszczenie oprogramowania pośredniczącego w dowolnym miejscu w potoku:
    * Punkty końcowe są wykonywane na pozycji <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> .
* Oprogramowanie pośredniczące terminalu umożliwia dowolny kod, aby określić, kiedy oprogramowanie pośredniczące jest zgodne:
    * Niestandardowy kod dopasowania trasy może być pełny i trudny do poprawnego zapisu.
    * Routing oferuje proste rozwiązania dla typowych aplikacji. Większość aplikacji nie wymaga niestandardowego kodu dopasowania trasy.
* Interfejs punktów końcowych z oprogramowaniemi pośredniczącymi, takimi jak `UseAuthorization` i `UseCors` .
    * Korzystanie z oprogramowania terminalowego z `UseAuthorization` `UseCors` systemem lub wymaga ręcznej obsługi systemu autoryzacji.

[Punkt końcowy](#endpoint) definiuje oba:

* Delegat do przetwarzania żądań.
* Kolekcja dowolnych metadanych. Metadane służą do implementowania zagadnień związanych z wycinaniem w oparciu o zasady i konfigurację dołączone do każdego punktu końcowego.

Oprogramowanie pośredniczące terminalu może być skutecznym narzędziem, ale może wymagać:

* Znaczna ilość kodu i testowania.
* Ręczna integracja z innymi systemami w celu osiągnięcia odpowiedniego poziomu elastyczności.

Rozważ integrację z routingiem przed zapisaniem oprogramowania pośredniczącego terminalu.

Istniejące oprogramowanie pośredniczące, które integruje się z usługą [map](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) lub <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> zwykle może być przełączane do punktu końcowego z obsługą routingu. [MapHealthChecks](https://github.com/aspnet/AspNetCore/blob/master/src/Middleware/HealthChecks/src/Builder/HealthCheckEndpointRouteBuilderExtensions.cs#L16) demonstruje wzorzec dla:
* Napisz metodę rozszerzenia <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder> .
* Utwórz zagnieżdżony potok oprogramowania pośredniczącego przy użyciu programu <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.CreateApplicationBuilder*> .
* Dołącz oprogramowanie pośredniczące do nowego potoku. W takim przypadku <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> .
* <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.Build*>Oprogramowanie pośredniczące w usłudze <xref:Microsoft.AspNetCore.Http.RequestDelegate> .
* Wywołaj `Map` i podaj nowy potok pośredniczący.
* Zwraca obiekt konstruktora dostarczony przez `Map` metodę rozszerzającą.

Poniższy kod przedstawia użycie [MapHealthChecks](xref:host-and-deploy/health-checks):

[!code-csharp[](routing/samples/3.x/RoutingSample/AuthorizationStartup.cs?name=snippet)]

Poprzedni przykład pokazuje, dlaczego zwrócenie obiektu konstruktora jest ważne. Zwrócenie obiektu konstruktora umożliwia deweloperowi aplikacji Konfigurowanie zasad, takich jak autoryzacja dla punktu końcowego. W tym przykładzie oprogramowanie do sprawdzania kondycji nie ma bezpośredniej integracji z systemem autoryzacji.

System metadanych został utworzony w odpowiedzi na problemy napotykane przez autorów rozszerzalności przy użyciu oprogramowania terminalu. Jest to problematyczne dla każdego oprogramowania pośredniczącego, aby wdrożyć własną integrację z systemem autoryzacji.

<a name="urlm"></a>

### <a name="url-matching"></a>Dopasowanie adresu URL

* To proces, za pomocą którego Routing dopasowuje żądanie przychodzące do [punktu końcowego](#endpoint).
* Jest oparty na danych w ścieżce URL i nagłówkach.
* Można rozszerzyć, aby uwzględnić dowolne dane w żądaniu.

Po zakończeniu routingu oprogramowanie pośredniczące ustawia `Endpoint` wartości i trasy do [funkcji żądania](xref:fundamentals/request-features) na <xref:Microsoft.AspNetCore.Http.HttpContext> podstawie bieżącego żądania:

* Wywołanie metody [HttpContext. GetEndPoint](<xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*>) Pobiera punkt końcowy.
* `HttpRequest.RouteValues`Pobiera kolekcję wartości tras.

[Oprogramowanie pośredniczące](xref:fundamentals/middleware/index) działające po kierowaniu oprogramowania pośredniczącego może sprawdzić punkt końcowy i podjąć odpowiednie działania. Na przykład, oprogramowanie pośredniczące autoryzacji może przejrzeć kolekcję metadanych punktu końcowego dla zasad autoryzacji. Gdy zostanie wykonane wszystkie oprogramowanie pośredniczące w potoku przetwarzania żądań, zostanie wywołany delegat wybranego punktu końcowego.

System routingu w ramach routingu punktów końcowych jest odpowiedzialny za wszystkie decyzje dotyczące wysyłania. Ponieważ oprogramowanie pośredniczące stosuje zasady oparte na wybranym punkcie końcowym, ważne jest, aby:

* Wszelkie decyzje, które mogą mieć wpływ na wysyłanie lub stosowanie zasad zabezpieczeń, odbywa się w systemie routingu.

> [!WARNING]
> W celu zapewnienia zgodności z poprzednimi wersjami, gdy Razor jest wykonywany delegat kontrolera lub stron, właściwości [RouteContext. RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) są ustawiane na odpowiednie wartości na podstawie przetwarzania żądania wykonanego do tej pory.
>
> `RouteContext`Typ zostanie oznaczony jako przestarzały w przyszłej wersji:
>
> * Migruj `RouteData.Values` do programu `HttpRequest.RouteValues` .
> * Migruj `RouteData.DataTokens` do pobierania [IDataTokensMetadata](xref:Microsoft.AspNetCore.Routing.IDataTokensMetadata) z metadanych punktu końcowego.

Dopasowywanie adresów URL działa w konfigurowalnym zestawie faz. W każdej fazie dane wyjściowe są zestawem dopasowań. Zestaw dopasowań można zawęzić dalej w następnej fazie. Implementacja routingu nie gwarantuje kolejności przetwarzania pasujących punktów końcowych. **Wszystkie** możliwe dopasowania są przetwarzane jednocześnie. Fazy dopasowywania adresów URL są wykonywane w następującej kolejności. ASP.NET Core:

1. Przetwarza ścieżkę URL względem zestawu punktów końcowych i ich szablonów tras, zbierając **wszystkie** dopasowania.
1. Pobiera poprzednią listę i usuwa dopasowania, które kończą się niepowodzeniem z zastosowaniem ograniczeń trasy.
1. Pobiera poprzednią listę i usuwa dopasowania, które kończą się niepowodzeniem zestawu wystąpień [MatcherPolicy](xref:Microsoft.AspNetCore.Routing.MatcherPolicy) .
1. Używa [EndpointSelector](xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector) , aby przeprowadzić ostateczną decyzję z poprzedniej listy.

Lista punktów końcowych jest określana według priorytetów według:

* [RouteEndpoint. Order](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order*)
* [Pierwszeństwo szablonu trasy](#rtp)

Wszystkie zgodne punkty końcowe są przetwarzane w każdej fazie aż do <xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector> osiągnięcia. `EndpointSelector`Jest to Faza końcowa. Wybiera punkt końcowy o najwyższym priorytecie z dopasowań w miarę najlepszego dopasowania. Jeśli istnieją inne dopasowania o takim samym priorytecie jak najlepsze dopasowanie, zostanie zgłoszony wyjątek niejednoznacznej zgodności.

Pierwszeństwo trasy jest obliczane na podstawie **bardziej szczegółowego** szablonu trasy o wyższym priorytecie. Rozważmy na przykład szablony `/hello` i `/{message}` :

* Oba pasują do ścieżki URL `/hello` .
* `/hello`jest bardziej szczegółowy i dlatego wyższy priorytet.

Ogólnie rzecz biorąc, pierwszeństwo trasy jest dobrym zadaniem do wyboru najlepszego dopasowania dla rodzajów schematów adresów URL używanych w ćwiczeniach. Użyj <xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order> tylko w razie potrzeby, aby uniknąć niejednoznaczności.

Ze względu na rodzaje rozszerzalności udostępniane przez usługę Routing nie jest możliwe, aby system routingu był obliczany przed czasem niejednoznacznych tras. Rozważmy przykład takich jak szablony tras `/{message:alpha}` i `/{message:int}` :

* `alpha`Ograniczenie pasuje tylko do znaków alfanumerycznych.
* `int`Ograniczenie jest zgodne tylko z liczbami.
* Te szablony mają ten sam priorytet trasy, ale nie istnieje pojedynczy adres URL, który oba są zgodne.
* Jeśli system routingu zgłosił błąd niejednoznaczności przy uruchamianiu, powinien zablokować ten prawidłowy przypadek użycia.

> [!WARNING]
>
> Kolejność operacji wewnątrz <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> nie wpływa na zachowanie routingu, z wyjątkiem jednego wyjątku. <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>i <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> Automatycznie przypisz wartość zamówienia do punktów końcowych na podstawie kolejności, w której są wywoływane. Symuluje to długotrwałe zachowanie kontrolerów bez systemu routingu, zapewniając takie same gwarancje jak starsze implementacje routingu.
>
> W starszej implementacji routingu możliwe jest wdrożenie rozszerzalności routingu, która ma zależność od kolejności przetwarzania tras. Routing punktów końcowych w ASP.NET Core 3,0 i nowszych:
> 
> * Nie ma koncepcji tras.
> * Nie oferuje gwarancji porządkowania. Wszystkie punkty końcowe są przetwarzane jednocześnie.
>
> Jeśli to oznacza, że używasz starszego systemu routingu, [Otwórz problem w usłudze GitHub, aby uzyskać pomoc](https://github.com/dotnet/aspnetcore/issues).

<a name="rtp"></a>

### <a name="route-template-precedence-and-endpoint-selection-order"></a>Kolejność zaznaczania szablonu i punktu końcowego

[Pierwszeństwo szablonu trasy](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L16) to system, który przypisuje każdemu szablonowi trasy wartość na podstawie ich określonego. Pierwszeństwo szablonu trasy:

* Pozwala uniknąć konieczności dostosowywania kolejności punktów końcowych w typowych przypadkach.
* Podejmuje próbę dopasowania częstych oczekiwań związanych z routingiem.

Rozważmy na przykład szablony `/Products/List` i `/Products/{id}` . Należy założyć, że `/Products/List` jest to lepszy odpowiednik niż `/Products/{id}` ścieżka URL `/Products/List` . Działa, ponieważ segment literału `/List` jest uznawany za wyższy niż segment parametru `/{id}` .

Szczegóły dotyczące sposobu działania pierwszeństwa są powiązane ze sposobem definiowania szablonów tras:

* Szablony o większej liczbie segmentów są uważane za bardziej szczegółowe.
* Segment z tekstem literału jest traktowany jako bardziej szczegółowy niż segment parametru.
* Segment parametru z ograniczeniem jest uznawany za bardziej szczegółowy niż jeden bez.
* Segment złożony jest uznawany za określony jako segment parametru z ograniczeniem.
* Catch-wszystkie parametry są najmniej określone. Aby uzyskać ważne informacje dotyczące przechwytywania wszystkich tras, zobacz temat **catch-all** w [dokumentacji dotyczącej szablonu trasy](#rtr) .

Zapoznaj się z [kodem źródłowym w witrynie GitHub](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L189) , aby uzyskać odwołanie do dokładnych wartości.

<a name="lg"></a>

### <a name="url-generation-concepts"></a>Pojęcia związane z generowaniem adresów URL

Generowanie adresu URL:

* To proces, za pomocą którego Routing może utworzyć ścieżkę URL na podstawie zestawu wartości trasy.
* Umożliwia logiczne rozdzielenie między punktami końcowymi i adresami URL, które uzyskują do nich dostęp.

Routing punktów końcowych obejmuje <xref:Microsoft.AspNetCore.Routing.LinkGenerator> interfejs API. `LinkGenerator`jest usługą pojedynczą dostępną w ramach programu [di](xref:fundamentals/dependency-injection). `LinkGenerator`Interfejsu API można używać poza kontekstem żądania wykonania. [MVC. IUrlHelper](xref:Microsoft.AspNetCore.Mvc.IUrlHelper) i scenariusze, które są zależne <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> , takie jak [pomocnicy tagów](xref:mvc/views/tag-helpers/intro), pomocników HTML i [wyniki akcji](xref:mvc/controllers/actions), używają `LinkGenerator` wewnętrznie interfejsu API w celu zapewnienia możliwości generowania linków.

Generator łącza jest objęty koncepcją i **schematami** **adresów.** Schemat adresów jest sposobem określania punktów końcowych, które należy wziąć pod uwagę podczas generowania łącza. Na przykład nazwa trasy i wartości trasy scenariusze wielu użytkowników są znane z kontrolerów, a Razor strony są implementowane jako schemat adresów.

Generator łącza może łączyć się z kontrolerami i Razor stronami przy użyciu następujących metod rozszerzających:

* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*>
* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetPathByPage*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetUriByPage*>

Przeciążenia tych metod akceptują argumenty, które obejmują `HttpContext` . Te metody są funkcjonalnie równoważne z [adresem URL. Action](xref:System.Web.Mvc.UrlHelper.Action*) i [URL. Page](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*), ale oferują dodatkową elastyczność i opcje.

`GetPath*`Metody są najbardziej podobne do `Url.Action` i `Url.Page` , w tym, że generują identyfikator URI zawierający ścieżkę bezwzględną. `GetUri*`Metody zawsze generują bezwzględny identyfikator URI zawierający schemat i hosta. Metody, które akceptują `HttpContext` Identyfikator URI w kontekście żądania wykonania. Użycie wartości tras w [otoczeniu](#ambient) , ścieżki podstawowej adresu URL, schematu i hosta z żądania wykonania jest używane, chyba że zostaną zastąpione.

<xref:Microsoft.AspNetCore.Routing.LinkGenerator>jest wywoływana przy użyciu adresu. Generowanie identyfikatora URI występuje w dwóch krokach:

1. Adres jest powiązany z listą punktów końcowych, które pasują do adresu.
1. Wszystkie punkty końcowe <xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern> są oceniane do momentu znalezienia wzorca tras pasującego do dostarczonych wartości. Wynikowe dane wyjściowe są łączone z innymi częściami identyfikatora URI dostarczanymi do generatora linków i zwracanymi.

Metody zapewniane przez <xref:Microsoft.AspNetCore.Routing.LinkGenerator> obsługę funkcji generowania linków standardowych dla dowolnego typu adresu. Najbardziej wygodnym sposobem korzystania z generatora łączy są metody rozszerzające, które wykonują operacje dla określonego typu adresu:

| Metoda rozszerzenia | Opis |
| ---
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-------- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

------ | | <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | Generuje identyfikator URI z ścieżką bezwzględną na podstawie podanych wartości. | | <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | Generuje bezwzględny identyfikator URI na podstawie podanych wartości.             |

> [!WARNING]
> Należy zwrócić uwagę na następujące konsekwencje wywoływania <xref:Microsoft.AspNetCore.Routing.LinkGenerator> metod:
>
> * Użyj `GetUri*` metod rozszerzających z zachowaniem ostrożności w konfiguracji aplikacji, która nie weryfikuje `Host` nagłówka żądań przychodzących. Jeśli `Host` nagłówek żądań przychodzących nie jest zweryfikowany, dane wejściowe żądania niezaufanego mogą być wysyłane z powrotem do klienta w identyfikatorach URI w widoku lub stronie. Zaleca się, aby wszystkie aplikacje produkcyjne skonfigurowali swój serwer do sprawdzania poprawności `Host` nagłówka pod kątem znanych prawidłowych wartości.
>
> * Należy używać <xref:Microsoft.AspNetCore.Routing.LinkGenerator> z zachowaniem ostrożności w oprogramowaniu pośredniczącym w połączeniu z `Map` lub `MapWhen` . `Map*`zmienia ścieżkę podstawową żądania wykonania, która ma wpływ na dane wyjściowe generowania łącza. Wszystkie <xref:Microsoft.AspNetCore.Routing.LinkGenerator> interfejsy API umożliwiają określanie ścieżki podstawowej. Określ pustą ścieżkę bazową, aby cofnąć `Map*` wpływ na generowanie linków.

### <a name="middleware-example"></a>Przykład oprogramowania pośredniczącego

W poniższym przykładzie oprogramowanie pośredniczące używa <xref:Microsoft.AspNetCore.Routing.LinkGenerator> interfejsu API do utworzenia linku do metody akcji, która wyświetla listę produktów ze sklepu. Przy użyciu generatora linków poprzez wstrzyknięcie go do klasy i wywołanie `GenerateLink` jest dostępne dla dowolnej klasy w aplikacji:

[!code-csharp[](routing/samples/3.x/RoutingSample/Middleware/ProductsLinkMiddleware.cs?name=snippet)]

<a name="rtr"></a>

## <a name="route-template-reference"></a>Odwołanie do szablonu trasy

Tokeny w ramach `{}` definiowania parametrów trasy, które są powiązane w przypadku dopasowania trasy. W segmencie trasy można zdefiniować więcej niż jeden parametr trasy, ale parametry tras muszą być rozdzielone wartością literału. Na przykład `{controller=Home}{action=Index}` nie jest prawidłową trasą, ponieważ nie ma wartości literału między `{controller}` i `{action}` .  Parametry trasy muszą mieć nazwę i mogą mieć określone dodatkowe atrybuty.

Tekst literału inny niż parametry trasy (na przykład `{id}` ) i separator ścieżki `/` muszą być zgodne z tekstem w adresie URL. W dopasowaniu do tekstu nie jest rozróżniana wielkość liter i w oparciu o zdekodowaną reprezentację ścieżki adresu URL. Aby dopasować Ogranicznik parametru trasy literału `{` lub `}` , należy wprowadzić ogranicznik, powtarzając znak. Na przykład `{{` lub `}}` .

Gwiazdka `*` lub podwójna gwiazdka `**` :

* Można go użyć jako prefiksu do parametru trasy, aby powiązać z pozostałą częścią identyfikatora URI.
* Są nazywane parametrami **przechwycenia** . Na przykład `blog/{**slug}` :
  * Dopasowuje dowolny identyfikator URI, który rozpoczyna się od `/blog` i ma dowolną wartość po nim.
  * Wartość poniżej `/blog` jest przypisana do wartości trasy [informacji](https://developer.mozilla.org/docs/Glossary/Slug) o sobie.

[!INCLUDE[](~/includes/catchall.md)]

Catch-wszystkie parametry można także dopasować do pustego ciągu.

Parametr catch-all wyprowadza odpowiednie znaki, gdy trasa jest używana do generowania adresu URL, włącznie z separatorami ścieżki `/` . Na przykład trasa `foo/{*path}` z wartościami trasy jest `{ path = "my/path" }` generowana `foo/my%2Fpath` . Zwróć uwagę na odwrócony ukośnik. Aby zaokrąglić znaki separatora ścieżki, użyj `**` prefiksu parametru trasy. Trasa `foo/{**path}` z `{ path = "my/path" }` generowaniem `foo/my/path` .

Wzorce adresów URL, które próbują przechwycić nazwę pliku z opcjonalnym rozszerzeniem pliku, mają dodatkowe uwagi. Rozważmy na przykład szablon `files/{filename}.{ext?}` . Gdy wartości obu `filename` i `ext` istnieją, są wypełniane obie wartości. Jeśli tylko wartość `filename` istnieje w adresie URL, trasa pasuje do siebie, ponieważ końcowe `.` jest opcjonalne. Następujące adresy URL pasują do tej trasy:

* `/files/myFile.txt`
* `/files/myFile`

Parametry trasy mogą mieć **wartości domyślne** , określając wartość domyślną po nazwie parametru oddzielone znakiem równości ( `=` ). Na przykład `{controller=Home}` definiuje `Home` jako wartość domyślną dla `controller` . Wartość domyślna jest używana, jeśli żadna wartość nie jest obecna w adresie URL dla parametru. Parametry trasy są opcjonalne przez dołączenie znaku zapytania ( `?` ) na końcu nazwy parametru. Na przykład `id?`. Różnica między wartościami opcjonalnymi i domyślnymi parametrami trasy to:

* Parametr trasy z wartością domyślną zawsze generuje wartość.
* Opcjonalny parametr ma wartość tylko wtedy, gdy wartość jest podana przez adres URL żądania.

Parametry trasy mogą mieć ograniczenia, które muszą być zgodne z wartością trasy powiązaną z adresem URL. Dodawanie `:` i ograniczanie nazwy po nazwie parametru trasy określa ograniczenie wbudowane dla parametru trasy. Jeśli ograniczenie wymaga argumentów, są one ujęte w nawiasy `(...)` po nazwie ograniczenia. Można określić wiele *ograniczeń wbudowanych* , dołączając inne `:` i nazwę ograniczenia.

Nazwa i argumenty ograniczenia są przekazane do usługi w <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> celu utworzenia wystąpienia <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> do użycia w przetwarzaniu adresów URL. Na przykład szablon trasy `blog/{article:minlength(10)}` określa `minlength` ograniczenie z argumentem `10` . Aby uzyskać więcej informacji na temat ograniczeń trasy i listę ograniczeń zapewnianych przez platformę, zobacz sekcję [odwołanie do ograniczenia trasy](#route-constraint-reference) .

Parametry trasy mogą także mieć Transformatory parametrów. Transformatory parametrów przekształcają wartość parametru podczas generowania linków i dopasowywania akcji i stron do adresów URL. Podobnie jak ograniczenia, transformatory parametrów można dodać do parametru trasy, dodając `:` nazwę i transformator po nazwie parametru trasy. Na przykład szablon trasy `blog/{article:slugify}` określa `slugify` transformator. Aby uzyskać więcej informacji na temat transformatorów parametrów, zobacz sekcję [informacje dotyczące transformatora parametrów](#parameter-transformer-reference) .

W poniższej tabeli przedstawiono przykładowe szablony tras i ich zachowanie:

| Szablon trasy                           | Przykładowy pasujący identyfikator URI    | Identyfikator URI żądania&hellip;                                                    |
| ---
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-------------------- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

------------ | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

------------------------------------- | | `hello`                                  | `/hello`                | Dopasowuje tylko jedną ścieżkę `/hello` .                                     | | `{Page=Home}`                            | `/`                     | Dopasowuje i ustawia `Page` jako `Home` .                                         | | `{Page=Home}`                            | `/Contact`              | Dopasowuje i ustawia `Page` jako `Contact` .                                      | | `{controller}/{action}/{id?}`            | `/Products/List`        | Mapuje do `Products` kontrolera i `List` akcji.                       | | `{controller}/{action}/{id?}`            | `/Products/Details/123` | Mapuje do `Products` kontrolera i `Details` akcji z `id` ustawioną na 123. | | `{controller=Home}/{action=Index}/{id?}` | `/`                     | Mapuje na `Home` kontroler i `Index` metodę. Parametr `id` jest ignorowany.        | | `{controller=Home}/{action=Index}/{id?}` | `/Products`         | Mapuje na `Products` kontroler i `Index` metodę. Parametr `id` jest ignorowany.        |

Użycie szablonu jest ogólnie najprostszym podejściem do routingu. Ograniczenia i wartości domyślne można także określić poza szablonem trasy.

### <a name="complex-segments"></a>Złożone segmenty

Złożone segmenty są przetwarzane przez dopasowanie ograniczników literałów od prawej do lewej w sposób [niezachłanney](#greedy) . Na przykład `[Route("/a{b}c{d}")]` jest segmentem złożonym.
Złożone segmenty działają w określony sposób, który należy zrozumieć, aby użyć ich pomyślnie. W przykładzie w tej sekcji pokazano, dlaczego złożone segmenty tylko naprawdę działają prawidłowo, gdy tekst ogranicznika nie jest wyświetlany wewnątrz wartości parametrów. Użycie [wyrażenia regularnego](/dotnet/standard/base-types/regular-expressions) , a następnie ręczne wyodrębnienie wartości jest zbędne dla bardziej złożonych przypadków.

[!INCLUDE[](~/includes/regex.md)]

Jest to podsumowanie kroków wykonywanych przez Routing z szablonem `/a{b}c{d}` i ścieżką URL `/abcd` . `|`Służy do wizualizacji działania algorytmu:

* Pierwszy literał, od prawej do lewej, jest `c` . `/abcd`W związku z tym jest wyszukiwany od prawej i odnaleziony `/ab|c|d` .
* Wszystko w prawo ( `d` ) jest teraz dopasowane do parametru Route `{d}` .
* Następnym literałem, od prawej do lewej, jest `a` . W związku z tym zostanie `/ab|c|d` wyszukany od momentu, gdy został pozostawiony, a następnie `a` zostanie znaleziony `/|a|b|c|d` .
* Wartość z prawej strony ( `b` ) jest teraz dopasowana do parametru Route `{b}` .
* Nie ma pozostałego tekstu i nie ma pozostałego szablonu trasy, więc jest to dopasowanie.

Oto przykład negatywnego przypadku przy użyciu tego samego szablonu `/a{b}c{d}` i ścieżki URL `/aabcd` . `|`Służy do wizualizacji działania algorytmu. Ten przypadek nie jest odpowiednikiem, który jest wyjaśniony przez ten sam algorytm:
* Pierwszy literał, od prawej do lewej, jest `c` . `/aabcd`W związku z tym jest wyszukiwany od prawej i odnaleziony `/aab|c|d` .
* Wszystko w prawo ( `d` ) jest teraz dopasowane do parametru Route `{d}` .
* Następnym literałem, od prawej do lewej, jest `a` . W związku z tym zostanie `/aab|c|d` wyszukany od momentu, gdy został pozostawiony, a następnie `a` zostanie znaleziony `/a|a|b|c|d` .
* Wartość z prawej strony ( `b` ) jest teraz dopasowana do parametru Route `{b}` .
* Na tym etapie jest pozostały tekst `a` , ale algorytm został uruchomiony z szablonu trasy do analizy, więc nie jest to dopasowanie.

Ponieważ algorytm dopasowywania nie jest [zachłanne](#greedy):

* Pasuje do najmniejszej ilości tekstu możliwej w każdym kroku.
* Każdy przypadek, w którym wartość ogranicznika pojawia się wewnątrz wartości parametrów, powoduje niedopasowanie.

Wyrażenia regularne zapewniają znacznie większą kontrolę nad ich zachowaniem.

<a name="greedy"></a>

Dopasowanie zachłanne, znane także jako [Dopasowywanie z opóźnieniem](https://wikipedia.org/wiki/Regular_expression#Lazy_matching), jest zgodne z największym możliwym ciągiem. Zachłanne jest zgodny z najmniejszym możliwym ciągiem.

## <a name="route-constraint-reference"></a>Odwołanie do ograniczenia trasy

Ograniczenia trasy są wykonywane, gdy nastąpiło dopasowanie do przychodzącego adresu URL, a Ścieżka adresu URL jest zgodna z wartościami trasy. Ograniczenia trasy zwykle sprawdzają wartość trasy skojarzoną za pośrednictwem szablonu trasy i podejmują decyzję o tym, czy wartość jest akceptowalna. Niektóre ograniczenia trasy używają danych poza wartością trasy, aby uwzględnić, czy żądanie może być kierowane. Na przykład <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> może akceptować lub odrzucać żądanie na podstawie jego zlecenia http. Ograniczenia są używane w żądaniach routingu i generowania linków.

> [!WARNING]
> Nie używaj ograniczeń sprawdzania poprawności danych wejściowych. Jeśli ograniczenia są używane do sprawdzania poprawności danych wejściowych, nieprawidłowe wyniki wejściowe w `404` odpowiedzi nie znaleziono. Nieprawidłowe dane wejściowe powinny generować `400` Nieprawidłowe żądanie z odpowiednim komunikatem o błędzie. Ograniczenia trasy są używane do odróżniania podobnych tras, a nie do sprawdzania danych wejściowych dla określonej trasy.

W poniższej tabeli przedstawiono przykładowe ograniczenia trasy i ich oczekiwane zachowanie:

| ograniczenie | Przykład | Przykładowe dopasowania | Uwagi |
| ---
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

----- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

---- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-------- | ----- | | `int` | `{id:int}` | `123456789`, `-123456789` | Dopasowuje dowolną liczbę całkowitą | | `bool` | `{active:bool}` | `true`, `FALSE` | Dopasowuje `true` lub `false` . Bez uwzględniania wielkości liter | | `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | Dopasowuje prawidłową `DateTime` wartość w niezmiennej kulturze. Zobacz poprzednie ostrzeżenie. | | `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Dopasowuje prawidłową `decimal` wartość w niezmiennej kulturze. Zobacz poprzednie ostrzeżenie. | | `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | Dopasowuje prawidłową `double` wartość w niezmiennej kulturze. Zobacz poprzednie ostrzeżenie. | | `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | Dopasowuje prawidłową `float` wartość w niezmiennej kulturze. Zobacz poprzednie ostrzeżenie. | | `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638` | Dopasowuje prawidłową `Guid` wartość | | `long`  |  `{ticks:long}`  |  `123456789` , `-123456789` | Pasuje do prawidłowej `long` wartości | | `minlength(value)`  |  `{username:minlength(4)}`  |  `Rick` | Ciąg musi składać się z co najmniej 4 znaków | | `maxlength(value)` | `{filename:maxlength(8)}` | `MyFile` | Ciąg nie może zawierać więcej niż 8 znaków | | `length(length)` | `{filename:length(12)}` | `somefile.txt` | Ciąg musi zawierać dokładnie 12 znaków | | `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | Ciąg musi zawierać od 8 do 16 znaków | | `min(value)` | `{age:min(18)}` | `19` | Wartość całkowita musi być co najmniej 18 | | `max(value)` | `{age:max(120)}` | `91` | Wartość całkowita nie może być większa niż 120 | | `range(min,max)` | `{age:range(18,120)}` | `91` | Wartość całkowita musi zawierać co najmniej 18, ale nie więcej niż 120 | | `alpha` | `{name:alpha}` | `Rick` | Ciąg musi składać się z co najmniej jednego znaku alfabetycznego `a` - `z` i nie uwzględnia wielkości liter. | | `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | Ciąg musi być zgodny z wyrażeniem regularnym. Zapoznaj się z poradami dotyczącymi definiowania wyrażenia regularnego. | | `required` | `{name:required}` | `Rick` | Służy do wymuszania, że podczas generowania adresu URL jest obecna wartość, która nie jest parametrem |

[!INCLUDE[](~/includes/regex.md)]

Wielokrotne ograniczenia rozdzielane średnikami mogą być stosowane do jednego parametru. Na przykład następujące ograniczenie ogranicza parametr do wartości całkowitej 1 lub wyższej:

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> Ograniczenia trasy, które weryfikują adres URL i są konwertowane na typ CLR zawsze używają niezmiennej kultury. Na przykład konwersja na typ CLR `int` lub `DateTime` . W tych ograniczeniach przyjęto założenie, że adres URL nie jest Lokalizowalny. Ograniczenia trasy dostarczone przez platformę nie modyfikują wartości przechowywanych w wartościach trasy. Wszystkie wartości tras analizowane na podstawie adresu URL są przechowywane jako ciągi. Na przykład, `float` ograniczenie próbuje przekonwertować wartość trasy na float, ale przekonwertowana wartość jest używana tylko do sprawdzenia, czy może być konwertowana na typ float.

### <a name="regular-expressions-in-constraints"></a>Wyrażenia regularne w ograniczeniach

[!INCLUDE[](~/includes/regex.md)]

Wyrażenia regularne można określać jako ograniczenia wbudowane przy użyciu `regex(...)` ograniczenia trasy. Metody w <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> rodzinie również akceptują literał obiektu w ograniczeniach. Jeśli formularz jest używany, wartości ciągów są interpretowane jako wyrażenia regularne.

Poniższy kod używa wbudowanego ograniczenia wyrażenia regularnego:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRegex.cs?name=snippet)]

Poniższy kod używa literału obiektu do określenia ograniczenia wyrażenia regularnego:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRegex2.cs?name=snippet)]

ASP.NET Core Framework dodaje `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` do konstruktora wyrażenia regularnego. <xref:System.Text.RegularExpressions.RegexOptions>Aby uzyskać opis tych elementów członkowskich, zobacz.

Wyrażenia regularne używają ograniczników i tokenów podobnie jak w przypadku routingu i języka C#. Tokeny wyrażenia regularnego muszą być zmienione. Aby użyć wyrażenia regularnego `^\d{3}-\d{2}-\d{4}$` w ograniczeniu wbudowanym, użyj jednego z następujących elementów:

* Zamień `\` znaki podane w ciągu jako `\\` znaki w pliku źródłowym C# w celu ucieczki `\` znaku ucieczki ciągu.
* [Literały ciągu Verbatim](/dotnet/csharp/language-reference/keywords/string).

Aby uzyskać ogranicznik parametrów routingu ucieczki,,,, `{` `}` `[` `]` podwójne znaki w wyrażeniu, na przykład, `{{` `}}` `[[` ,, `]]` . W poniższej tabeli przedstawiono wyrażenie regularne i jego wersja o zmienionym znaczeniu:

| Wyrażenie regularne    | Wyrażenie regularne o zmienionym znaczeniu     |
| ---
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

----------- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

--------------- | | `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

Wyrażenia regularne używane w routingu często zaczynają się od `^` znaku i pasują do pozycji początkowej ciągu. Wyrażenia często kończą się `$` znakiem i pasują do końca ciągu. `^`Znaki i `$` zapewniają, że wyrażenie regularne dopasowuje całą wartość parametru trasy. Bez `^` znaków i `$` wyrażenie regularne dopasowuje dowolny podciąg w ciągu, co jest często niepożądane. W poniższej tabeli przedstawiono przykłady i wyjaśniono, dlaczego są one zgodne lub nie można ich dopasować:

| Wyrażenie   | String (ciąg)    | Dopasowanie | Komentarz               |
| ---
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

------ | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

----- | :---: |  ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

---------- | | `[a-z]{2}`   | Witaj | Tak | Dopasowania podciągów | | `[a-z]{2}`   | 123abc456 | Tak | Dopasowania podciągów | | `[a-z]{2}`   | MZ | Tak | Wyrażenie dopasowania | | `[a-z]{2}`   | MZ | Tak | Bez uwzględniania wielkości liter | | `^[a-z]{2}$` | Witaj | Nie | Zobacz `^` i `$` powyżej | | `^[a-z]{2}$` | 123abc456 | Nie | Zobacz `^` i `$` powyżej |

Aby uzyskać więcej informacji na temat składni wyrażeń regularnych, zobacz [.NET Framework wyrażeń regularnych](/dotnet/standard/base-types/regular-expression-language-quick-reference).

Aby ograniczyć parametr do znanego zestawu możliwych wartości, użyj wyrażenia regularnego. Na przykład `{action:regex(^(list|get|create)$)}` dopasowuje `action` wartość trasy do `list` , `get` , lub `create` . Jeśli przeszedł do słownika ograniczeń, ciąg `^(list|get|create)$` jest równoważny. Ograniczenia, które są przesyłane w słowniku ograniczeń, które nie pasują do jednego ze znanych ograniczeń, są również traktowane jako wyrażenia regularne. Ograniczenia, które są przesyłane w ramach szablonu, który nie odpowiada jednemu ze znanych ograniczeń, nie są traktowane jako wyrażenia regularne.

### <a name="custom-route-constraints"></a>Niestandardowe ograniczenia trasy

Niestandardowe ograniczenia trasy można utworzyć przez implementację <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> interfejsu. `IRouteConstraint`Interfejs zawiera <xref:System.Web.Routing.IRouteConstraint.Match*> , który zwraca, `true` Jeśli ograniczenie jest spełnione i `false` w przeciwnym razie.

Niestandardowe ograniczenia trasy są rzadko zbędne. Przed wdrożeniem niestandardowego ograniczenia trasy należy rozważyć alternatywy, takie jak powiązanie modelu.

Folder [ograniczenia](https://github.com/dotnet/aspnetcore/tree/master/src/Http/Routing/src/Constraints) ASP.NET Core zawiera dobre przykłady tworzenia ograniczeń. Na przykład [GuidRouteConstraint](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Constraints/GuidRouteConstraint.cs#L18).

Aby użyć niestandardowego `IRouteConstraint` , typ ograniczenia trasy musi być zarejestrowany w ramach aplikacji <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> w kontenerze usługi. `ConstraintMap`Jest słownikiem, który mapuje klucze ograniczeń trasy do `IRouteConstraint` implementacji, które weryfikują te ograniczenia. Aplikację `ConstraintMap` można zaktualizować w `Startup.ConfigureServices` ramach [usług. Wywołanie addrouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) lub przez skonfigurowanie <xref:Microsoft.AspNetCore.Routing.RouteOptions> bezpośrednio w usłudze `services.Configure<RouteOptions>` . Przykład:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint.cs?name=snippet)]

Poprzednie ograniczenie jest stosowane w poniższym kodzie:

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/TestController.cs?name=snippet&highlight=6,13)]

[!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

Implementacja `MyCustomConstraint` uniemożliwia `0` stosowanie do parametru trasy:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint.cs?name=snippet2)]

[!INCLUDE[](~/includes/regex.md)]

Powyższy kod ma następujące działanie:

* Zapobiega `0` w `{id}` segmencie trasy.
* Jest przedstawiany jako podstawowy przykład implementowania niestandardowego ograniczenia. Nie powinno być używane w aplikacji produkcyjnej.

Poniższy kod jest lepszym rozwiązaniem do zapobiegania `id` `0` przetwarzaniu danych z programu.

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/TestController.cs?name=snippet2)]

Poprzedni kod ma następujące zalety w porównaniu z `MyCustomConstraint` podejściem:

* Nie wymaga ograniczeń niestandardowych.
* Zwraca bardziej opisowy błąd, gdy parametr Route zawiera `0` .

## <a name="parameter-transformer-reference"></a>Odwołanie do transformatora parametrów

Transformatory parametrów:

* Wykonaj podczas generowania linku przy użyciu <xref:Microsoft.AspNetCore.Routing.LinkGenerator> .
* Implementacja <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer?displayProperty=fullName> .
* Są konfigurowane przy użyciu <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> .
* Wypełnij wartość trasy parametru i Przekształć ją na nową wartość ciągu.
* Wynikiem jest użycie przekształconej wartości w wygenerowanym łączu.

Na przykład niestandardowy `slugify` transformator parametrów w wzorcu trasy `blog\{article:slugify}` z `Url.Action(new { article = "MyTestArticle" })` generowaniem `blog\my-test-article` .

Weź pod uwagę następującą `IOutboundParameterTransformer` implementację:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint2.cs?name=snippet2)]

Aby użyć transformatora parametrów w wzorcu trasy, skonfiguruj go przy użyciu <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> w programie `Startup.ConfigureServices` :

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint2.cs?name=snippet)]

ASP.NET Core Framework używa transformatorów parametrów do przekształcania identyfikatora URI, w którym jest rozpoznawany punkt końcowy. Na przykład Transformatory parametrów przekształcają wartości tras używane do dopasowania do `area` , `controller` , `action` , i `page` .

```csharp
routes.MapControllerRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

W przypadku poprzedniego szablonu trasy Akcja `SubscriptionManagementController.GetAll` jest zgodna z identyfikatorem URI `/subscription-management/get-all` . Transformator parametrów nie zmienia wartości trasy użytych do wygenerowania linku. Na przykład dane `Url.Action("GetAll", "SubscriptionManagement")` wyjściowe `/subscription-management/get-all` .

ASP.NET Core udostępnia konwencje interfejsu API do korzystania z transformatorów parametrów z wygenerowanymi trasami:

* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention?displayProperty=fullName>Konwencja MVC stosuje określony transformator parametrów do wszystkich tras atrybutów w aplikacji. Transformator parametrów przekształca tokeny trasy atrybutów po ich wymianie. Aby uzyskać więcej informacji, zobacz [używanie transformatora parametrów do dostosowywania zastępowania tokenu](xref:mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement).
* RazorStrony używają <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention> Konwencji interfejsu API. Ta Konwencja stosuje określony transformator parametrów do wszystkich automatycznie odnalezionych Razor stron. Transformator parametrów przekształca folder i segmenty nazw plików Razor tras. Aby uzyskać więcej informacji, zobacz [używanie transformatora parametrów do dostosowywania tras stron](xref:razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes).

<a name="ugr"></a>

## <a name="url-generation-reference"></a>Odwołanie do generacji adresów URL

Ta sekcja zawiera odwołanie do algorytmu zaimplementowane przez generowanie adresów URL. W tym przypadku najbardziej złożone przykłady generowania adresów URL używają kontrolerów lub Razor stron. Aby uzyskać dodatkowe informacje, zobacz [Routing na kontrolerach](xref:mvc/controllers/routing) .

Proces generowania adresu URL rozpoczyna się od wywołania metody [LinkGenerator. GetPathByAddress](xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*) lub podobną metodę. Metoda jest dostarczana z adresem, zestawem wartości tras i opcjonalnie informacjami o bieżącym żądaniu z `HttpContext` .

Pierwszym krokiem jest użycie adresu do rozpoznania zestawu punktów końcowych kandydujących przy użyciu [`IEndpointAddressScheme<TAddress>`](xref:Microsoft.AspNetCore.Routing.IEndpointAddressScheme`1) , który jest zgodny z typem adresu.

Po znalezieniu zestawu kandydatów przez schemat adresów punkty końcowe są uporządkowane i przetwarzane iteracyjnie do momentu pomyślnego zakończenia operacji generowania adresu URL. Generowanie adresu URL **nie sprawdza niejasności** . pierwszy zwrócony wynik to wynik końcowy.

### <a name="troubleshooting-url-generation-with-logging"></a>Rozwiązywanie problemów z generowaniem adresów URL przy użyciu rejestrowania

Pierwszym krokiem podczas generowania adresu URL rozwiązywania problemów jest ustawienie poziomu rejestrowania `Microsoft.AspNetCore.Routing` na `TRACE` . `LinkGenerator`rejestruje wiele szczegółowych informacji o jego przetwarzaniu, które mogą być przydatne do rozwiązywania problemów.

Aby uzyskać szczegółowe informacje na temat generowania adresów URL, zobacz [odwołanie do generacji adresów URL](#ugr) .

### <a name="addresses"></a>Addresses (Adresy)

Adresy są pojęciem generacji adresów URL używanym do powiązania wywołania z generatorem linków z zestawem punktów końcowych kandydatów.

Adresy to rozszerzalne koncepcje, które domyślnie mają dwie implementacje:

* Użycie *nazwy punktu końcowego* ( `string` ) jako adresu:
    * Zapewnia podobną funkcjonalność do nazwy trasy MVC.
    * Używa <xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata> typu metadanych.
    * Rozpoznaje podany ciąg w odniesieniu do metadanych wszystkich zarejestrowanych punktów końcowych.
    * Zgłasza wyjątek podczas uruchamiania, jeśli wiele punktów końcowych używa tej samej nazwy.
    * Zalecane do zastosowania ogólnego przeznaczenia poza kontrolerami i Razor stronami.
* Użycie *wartości trasy* ( <xref:Microsoft.AspNetCore.Routing.RouteValuesAddress> ) jako adresu:
    * Zapewnia podobną funkcjonalność do kontrolerów i stron, które są Razor starszej generacji adresów URL.
    * Bardzo skomplikowane do rozszerania i debugowania.
    * Zapewnia implementację używaną przez `IUrlHelper` , pomocników tagów, pomocników HTML, wyników akcji itp.

Rolą schematu adresowania jest skojarzenie między adresem i zgodnymi punktami końcowymi przez dowolne kryterium:

* Schemat nazwy punktu końcowego wykonuje wyszukiwanie podstawowego słownika.
* Schemat wartości trasy ma złożone najlepsze podzbiór algorytmu zestawu.

<a name="ambient"></a>

### <a name="ambient-values-and-explicit-values"></a>Wartości otoczenia i jawne wartości

Z bieżącego żądania Routing uzyskuje dostęp do wartości trasy bieżącego żądania `HttpContext.Request.RouteValues` . Wartości skojarzone z bieżącym żądaniem są określane jako **wartości otoczenia**. Na potrzeby przejrzystości dokumentacja odwołuje się do wartości trasy przekazaną do metod jako **wartości jawnych**.

Poniższy przykład pokazuje wartości otoczenia i jawne wartości. Zapewnia ona wartości otoczenia z bieżącego żądania i wartości jawne: `{ id = 17, }` :

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet)]

Powyższy kod ma następujące działanie:

* Typu`/Widget/Index/17`
* Pobiera <xref:Microsoft.AspNetCore.Routing.LinkGenerator> za pośrednictwem [di](xref:fundamentals/dependency-injection).

Poniższy kod nie zawiera żadnych wartości otoczenia i wartości jawnych: `{ controller = "Home", action = "Subscribe", id = 17, }` :

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet2)]

Poprzednia metoda zwraca`/Home/Subscribe/17`

Następujący kod w `WidgetController` zwracaniu `/Widget/Subscribe/17` :

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet3)]

Poniższy kod udostępnia kontroler z wartości otoczenia w bieżącym żądaniu i wartościach jawnych: `{ action = "Edit", id = 17, }` :

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/GadgetController.cs?name=snippet)]

Powyższy kod ma następujące działanie:

* `/Gadget/Edit/17`jest zwracany.
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Url>Pobiera <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> .
* <xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Action*>   
generuje adres URL ze ścieżką bezwzględną dla metody akcji. Adres URL zawiera określoną `action` nazwę i `route` wartości.

Poniższy kod zawiera wartości otoczenia z bieżącego żądania oraz wartości jawne: `{ page = "./Edit, id = 17, }` :

[!code-csharp[](routing/samples/3.x/RoutingSample/Pages/Index.cshtml.cs?name=snippet)]

Poprzedni kod jest ustawiany `url` na, `/Edit/17` gdy Razor Strona Edycja zawiera następującą dyrektywę strony:

 `@page "{id:int}"`

Jeśli strona Edycja nie zawiera `"{id:int}"` szablonu trasy, `url` to `/Edit?id=17` .

Zachowanie funkcji MVC <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> dodaje warstwę złożoności obok zasad opisanych tutaj:

* `IUrlHelper`zawsze udostępnia wartości trasy z bieżącego żądania jako wartości otoczenia.
* [IUrlHelper. Action](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Action*) zawsze kopiuje bieżące `action` i `controller` trasy jako wartości jawne, chyba że zostaną zastąpione przez dewelopera.
* [IUrlHelper. Page](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*) zawsze kopiuje bieżącą `page` wartość trasy jako wartość jawną, chyba że zostanie zastąpiona. <!--by the user-->
* `IUrlHelper.Page`zawsze zastępuje bieżącą `handler` wartość trasy wartością `null` jako jawne wartości, chyba że zostaną zastąpione.

Użytkownicy są często przeniesieni przez szczegółowe informacje o zachowaniu wartości otoczenia, ponieważ MVC nie przestrzega własnych reguł. Ze względu na historyczne i zgodność niektóre wartości tras, takie jak `action` ,, `controller` `page` i `handler` mają własne zachowanie specjalne.

Równoważne funkcje zapewniane przez program `LinkGenerator.GetPathByAction` i `LinkGenerator.GetPathByPage` duplikują te anomalie `IUrlHelper` w celu zapewnienia zgodności.

### <a name="url-generation-process"></a>Proces generowania adresu URL

Po znalezieniu zestawu punktów końcowych kandydujących algorytm generowania adresów URL:

* Przetwarza punkty końcowe iteracyjnie.
* Zwraca pierwszy udany wynik.

Pierwszy krok w tym procesie nazywa się **nieprawidłową wartością trasy**.  Unieważnianie wartości trasy to proces, za pomocą którego Routing decyduje o tym, które wartości tras z wartości otoczenia powinny być używane, i które mają być ignorowane. Każda wartość otoczenia jest brana pod uwagę i jest dołączona do wartości jawnych lub ignorowana.

Najlepszym sposobem, aby myśleć o roli otaczających wartości, jest próba zapisu deweloperów aplikacji wpisywanych w niektórych typowych przypadkach. Tradycyjnie scenariusze, w których wartości otoczenia są przydatne, są związane z MVC:

* Podczas łączenia z inną akcją w tym samym kontrolerze nie trzeba określać nazwy kontrolera.
* W przypadku łączenia z innym kontrolerem w tym samym obszarze nie trzeba określać nazwy obszaru.
* Podczas łączenia z tą samą metodą akcji nie trzeba określać wartości trasy.
* Podczas tworzenia połączenia z inną częścią aplikacji nie ma potrzeby przenoszenia wartości tras, które nie mają znaczenia w tej części aplikacji.

Wywołania do `LinkGenerator` lub `IUrlHelper` zwracane `null` są zwykle spowodowane nieprawidłowym unieważnieniem wartości trasy. Rozwiązywanie problemów dotyczących unieważniania wartości trasy przez jawne określenie większej liczby wartości trasy, aby sprawdzić, czy rozwiązanie to rozwiązuje problem.

Unieważnianie wartości trasy działa zgodnie z założeniem, że schemat adresu URL aplikacji jest hierarchiczny, z hierarchią utworzoną od lewej do prawej. Rozważmy szablon trasy podstawowego kontrolera, `{controller}/{action}/{id?}` Aby uzyskać intuicyjny opis tego, jak to działa. **Zmiana** wartości powoduje **unieważnienie** wszystkich wartości tras, które pojawiają się po prawej stronie. Odzwierciedla to założenie hierarchii. Jeśli aplikacja ma wartość otoczenia dla `id` , a operacja określa inną wartość dla `controller` :

* `id`nie będzie ponownie używany `{controller}` , ponieważ jest z lewej strony `{id?}` .

Przykłady ukazujące tę zasadę:

* Jeśli jawne wartości zawierają wartość dla `id` , wartość otoczenia dla `id` jest ignorowana. Wartości otoczenia dla `controller` i `action` mogą być używane.
* Jeśli jawne wartości zawierają wartość dla `action` , jakakolwiek wartość otoczenia dla `action` jest ignorowana. Można użyć wartości otoczenia `controller` . Jeśli wartość Explicit dla `action` jest inna niż wartość otoczenia dla `action` , `id` wartość nie zostanie użyta.  Jeśli wartość Explicit dla `action` jest taka sama jak wartość otoczenia dla `action` , `id` można użyć wartości.
* Jeśli jawne wartości zawierają wartość dla `controller` , jakakolwiek wartość otoczenia dla `controller` jest ignorowana. Jeśli wartość Explicit dla różni `controller` się od wartości otoczenia dla `controller` , `action` wartości i nie będą `id` używane. Jeśli wartość Explicit dla `controller` jest taka sama jak wartość otoczenia dla `controller` , `action` `id` można użyć wartości i.

Ten proces jest bardziej skomplikowany przez istnienie tras atrybutów i dedykowanych tras konwencjonalnych. Konwencjonalne trasy kontrolerów, takie jak `{controller}/{action}/{id?}` Określanie hierarchii przy użyciu parametrów trasy. W przypadku [dedykowanych tras konwencjonalnych](xref:mvc/controllers/routing#dcr) i [tras atrybutów](xref:mvc/controllers/routing#ar) do kontrolerów i Razor stron:

* Istnieje hierarchia wartości tras.
* Nie są one wyświetlane w szablonie.

W takich przypadkach generowanie adresów URL definiuje **wymagane wartości** koncepcji. Punkty końcowe utworzone przez kontrolery i Razor strony mają określone wartości, które zezwalają na działanie unieważniania wartości trasy.

Nieszczegółowy algorytm unieważniania wartości trasy:

* Nazwy wymaganych wartości są łączone z parametrami trasy, a następnie przetwarzane od lewej do prawej.
* Dla każdego parametru wartość otoczenia i wartość jawna są porównywane:
    * Jeśli wartość otoczenia i wartość jawna są takie same, proces jest kontynuowany.
    * Jeśli wartość otoczenia jest obecna i wartość jawna nie jest, podczas generowania adresu URL zostanie użyta wartość otoczenia.
    * Jeśli wartość otoczenia nie jest obecna, a wartość jawna to, Odrzuć wartość otoczenia i wszystkie kolejne wartości otoczenia.
    * Jeśli wartość otoczenia i wartość jawna są obecne, a dwie wartości są różne, Odrzuć wartość otoczenia i wszystkie kolejne wartości otoczenia.

W tym momencie operacja generowania adresu URL jest gotowa do oszacowania ograniczeń trasy. Zestaw zaakceptowanych wartości jest połączony z wartościami domyślnymi parametrów, które są dostarczane do ograniczeń. Jeśli wszystkie ograniczenia są przekazywane, operacja będzie kontynuowana.

Następnie **zaakceptowane wartości** mogą służyć do rozwinięcia szablonu trasy. Szablon trasy jest przetwarzany:

* Od lewej do prawej.
* Każdy parametr ma zaakceptowaną wartość.
* W przypadku następujących specjalnych przypadków:
  * Jeśli w zaakceptowanych wartości brakuje wartości, a parametr ma wartość domyślną, zostanie użyta wartość domyślna.
  * Jeśli w zaakceptowanych wartości brakuje wartości, a parametr jest opcjonalny, przetwarzanie jest kontynuowane.
  * Jeśli którykolwiek z parametrów trasy po prawej stronie brakującego parametru opcjonalnego ma wartość, operacja kończy się niepowodzeniem.
  * <!-- review default-valued parameters optional parameters --> W miarę możliwości są zwijane ciągłe parametry wartości domyślnych i parametry opcjonalne.

Wartości jawnie podane, które nie pasują do segmentu trasy, są dodawane do ciągu zapytania. W poniższej tabeli przedstawiono wynik przy użyciu szablonu trasy `{controller}/{action}/{id?}` .

| Wartości otoczenia                     | Wartości jawne                        | Wynik                  |
| ---
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

----------------- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

------------------- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

------------ | | Controller = "Strona główna" | Action = "informacje" | `/Home/About`|
| Controller = "Strona główna" | Controller = "Order", Action = "informacje" | `/Order/About`|
| Controller = "Home", Color = "Red" | Action = "informacje" | `/Home/About`|
| Controller = "Strona główna" | Action = "informacje", Color = "Red" | `/Home/About?color=Red`                                |

### <a name="problems-with-route-value-invalidation"></a>Problemy z nieprawidłową wartością trasy

W ASP.NET Core 3,0 niektóre schematy generacji adresów URL używane we wcześniejszych wersjach ASP.NET Core nie działają poprawnie z generowaniem adresów URL. Zespół ASP.NET Core planuje dodać funkcje, aby rozwiązać te potrzeby w przyszłej wersji. Teraz najlepszym rozwiązaniem jest użycie starszego routingu.

Poniższy kod przedstawia przykładowy Schemat generowania adresu URL, który nie jest obsługiwany przez Routing.

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupUnsupported.cs?name=snippet)]

W powyższym kodzie, `culture` parametr trasy jest używany do lokalizacji. Chcemy, aby parametr był `culture` zawsze akceptowany jako wartość otoczenia. Jednak `culture` parametr nie jest akceptowany jako wartość otoczenia ze względu na sposób działania wymaganych wartości:

* W `"default"` szablonie trasy `culture` parametr trasy jest z lewej strony `controller` , więc zmiany `controller` nie zostaną unieważnione `culture` .
* W `"blog"` szablonie trasy `culture` parametr trasy jest uznawany za z prawej strony `controller` , która jest wyświetlana w wymaganych wartościach.

## <a name="configuring-endpoint-metadata"></a>Konfigurowanie metadanych punktu końcowego

Poniższe linki zawierają informacje dotyczące konfigurowania metadanych punktu końcowego:

* [Włączanie mechanizmu CORS przy użyciu routingu punktu końcowego](xref:security/cors#enable-cors-with-endpoint-routing)
* [Przykład IAuthorizationPolicyProvider](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/CustomPolicyProvider) przy użyciu `[MinimumAgeAuthorize]` atrybutu niestandardowego
* [Testowanie uwierzytelniania przy użyciu atrybutu [autoryzuje]](xref:security/authentication/identity#test-identity)
* <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization*>
* [Wybieranie schematu z atrybutem [autoryzuje]](xref:security/authorization/limitingidentitybyscheme#selecting-the-scheme-with-the-authorize-attribute)
* [Stosowanie zasad przy użyciu atrybutu [autoryzuje]](xref:security/authorization/policies#applying-policies-to-mvc-controllers)
* <xref:security/authorization/roles>

<a name="hostmatch"></a>

## <a name="host-matching-in-routes-with-requirehost"></a>Pasujące hosty w trasach z RequireHost

<xref:Microsoft.AspNetCore.Builder.RoutingEndpointConventionBuilderExtensions.RequireHost*>stosuje ograniczenie do trasy wymagającej określonego hosta. `RequireHost`Parametrem lub [[Host]](xref:Microsoft.AspNetCore.Routing.HostAttribute) może być:

* Host: `www.domain.com` , dopasowuje `www.domain.com` się do dowolnego portu.
* Host z symbolami wieloznacznymi: `*.domain.com` , dopasowań `www.domain.com` , `subdomain.domain.com` lub `www.subdomain.domain.com` na dowolnym porcie.
* Port: `*:5000` , jest zgodny z portem 5000 z dowolnym hostem.
* Host i port: `www.domain.com:5000` lub `*.domain.com:5000` , dopasowuje hosta i port.

Można określić wiele parametrów przy użyciu `RequireHost` lub `[Host]` . Ograniczenie jest zgodne z hostami prawidłowymi dla któregokolwiek z parametrów. Na przykład `[Host("domain.com", "*.domain.com")]` dopasowuje `domain.com` , `www.domain.com` , i `subdomain.domain.com` .

Następujący kod używa `RequireHost` do żądania określonego hosta trasy:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRequireHost.cs?name=snippet)]

Poniższy kod używa `[Host]` atrybutu na kontrolerze, aby wymagać któregokolwiek z określonych hostów:

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/ProductController.cs?name=snippet)]

Gdy `[Host]` atrybut jest stosowany do obu metod:

* Atrybut akcji jest używany.
* Atrybut Controller jest ignorowany.

## <a name="performance-guidance-for-routing"></a>Wskazówki dotyczące wydajności routingu

Większość routingu została zaktualizowana w ASP.NET Core 3,0, aby zwiększyć wydajność.

Gdy aplikacja ma problemy z wydajnością, routing jest często podejrzany o problem. Podejrzenie, że Routing jest podejrzany, że platformy, takie jak kontrolery i Razor strony, zgłaszają czas spędzony w ramach struktury w swoich komunikatach rejestrowania. Gdy istnieje znacząca różnica między czasem zgłoszonym przez kontrolery i łącznym czasem żądania:

* Deweloperzy eliminują kod aplikacji jako źródło problemu.
* Zakłada się, że kierowanie jest przyczyną.

Routing jest testowaną wydajnością przy użyciu tysięcy punktów końcowych. Jest mało prawdopodobne, że w typowej aplikacji wystąpi problem z wydajnością, tylko jest zbyt duży. Najbardziej powszechną przyczyną niskiej wydajności routingu jest zwykle nieprawidłowo działające niestandardowe oprogramowanie pośredniczące.

Poniższy przykładowy kod demonstruje podstawową technikę zawężania źródła opóźnienia:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupDelay.cs?name=snippet)]

Do routingu czasu:

* Pozostawij każde oprogramowanie pośredniczące z kopią oprogramowania pośredniczącego w poprzednim kodzie.
* Dodaj unikatowy identyfikator w celu skorelowania danych chronometrażu z kodem.

Jest to podstawowy sposób na zawężenie opóźnień, gdy jest to istotne, na przykład więcej niż `10ms` .  Odejmowanie `Time 2` od `Time 1` raportów czasu spędzonego w oprogramowaniu `UseRouting` pośredniczącym.

Poniższy kod używa bardziej kompaktowego podejścia do poprzedniego kodu czasu:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupSW.cs?name=snippetSW)]

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupSW.cs?name=snippet)]

### <a name="potentially-expensive-routing-features"></a>Potencjalnie kosztowna Funkcja routingu

Poniższa lista zawiera szczegółowe informacje o funkcjach routingu, które są stosunkowo kosztowne w porównaniu z podstawowymi szablonami tras:

* Wyrażenia regularne: można pisać wyrażenia regularne, które są złożone, lub długi czas wykonywania z niewielką ilością danych wejściowych.

* Złożone segmenty ( `{x}-{y}-{z}` ): 
  * Są znacznie droższe niż analizowanie zwykłego segmentu ścieżki URL.
  * Powoduje przydzielenie wielu podciągów.
  * Logika złożonego segmentu nie została zaktualizowana w ramach aktualizacji wydajności ASP.NET Core 3,0.

* Synchroniczny dostęp do danych: wiele złożonych aplikacji ma dostęp do bazy danych w ramach ich routingu. ASP.NET Core 2,2 i wcześniejsze routinge mogą nie zapewniać odpowiednich punktów rozszerzalności do obsługi routingu dostępu do bazy danych. Na przykład, <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> i <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> są synchroniczne. Punkty rozszerzalności, takie jak <xref:Microsoft.AspNetCore.Routing.MatcherPolicy> i, <xref:Microsoft.AspNetCore.Routing.EndpointSelectorContext> są asynchroniczne.

## <a name="guidance-for-library-authors"></a>Wskazówki dla autorów biblioteki

Ta sekcja zawiera wskazówki dla autorów biblioteki, które są tworzone w oparciu o Routing. Te szczegóły są przeznaczone do zapewnienia, że deweloperzy aplikacji mają dobre doświadczenie przy użyciu bibliotek i struktur, które poszerzają Routing.

### <a name="define-endpoints"></a>Definiuj punkty końcowe

Aby utworzyć platformę, która używa routingu do dopasowywania adresów URL, Zacznij od zdefiniowania środowiska użytkownika, które kompiluje się w górę <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> .

**Wykonaj** kompilację w usłudze <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder> . Dzięki temu użytkownicy mogą redagować swoją strukturę przy użyciu innych funkcji ASP.NET Core bez pomyłek. Każdy szablon ASP.NET Core obejmuje Routing. Załóżmy, że Routing jest obecny i znany użytkownikom.

```csharp
app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...);

    endpoints.MapHealthChecks("/healthz");
});
```

**Należy** zwrócić zapieczętowany Typ konkretny z wywołania do `MapMyFramework(...)` tego implementującego <xref:Microsoft.AspNetCore.Builder.IEndpointConventionBuilder> . Większość metod struktury jest `Map...` zgodna z tym wzorcem. `IEndpointConventionBuilder`Interfejs:

* Umożliwia tworzenie metadanych.
* Jest przeznaczony dla różnych metod rozszerzających.

Deklarowanie własnego typu pozwala na dodanie własnych funkcji specyficznych dla platformy do konstruktora. Jest to możliwe, aby otoczyć Konstruktor zadeklarowany przez platformę i przekazać do niego wywołania.

```csharp
app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...).RequrireAuthorization()
                                 .WithMyFrameworkFeature(awesome: true);

    endpoints.MapHealthChecks("/healthz");
});
```

**Rozważ** napisanie własnych <xref:Microsoft.AspNetCore.Routing.EndpointDataSource> . `EndpointDataSource`to element podstawowy niskiego poziomu służący do deklarowania i aktualizowania kolekcji punktów końcowych. `EndpointDataSource`to zaawansowany interfejs API używany przez kontrolery i Razor strony.

Testy routingu mają [podstawowy przykład](https://github.com/aspnet/AspNetCore/blob/master/src/Http/Routing/test/testassets/RoutingSandbox/Framework/FrameworkEndpointDataSource.cs#L17) źródła danych, które nie są aktualizowane.

**Nie** należy próbować rejestrować `EndpointDataSource` domyślnie. Wymagaj od użytkowników zarejestrowania struktury w programie <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> . Wyznaczanie trasy polega na tym, że nic nie jest uwzględniane domyślnie, a `UseEndpoints` to miejsce do rejestrowania punktów końcowych.

### <a name="creating-routing-integrated-middleware"></a>Tworzenie oprogramowania pośredniczącego zintegrowanego z routingiem

**Należy rozważyć** Definiowanie typów metadanych jako interfejsu.

**Czy** można używać typów metadanych jako atrybutu dla klas i metod.

[!code-csharp[](routing/samples/3.x/RoutingSample/ICoolMetadata.cs?name=snippet2)]

Platformy, takie jak kontrolery i Razor strony, obsługują stosowanie atrybutów metadanych do typów i metod. W przypadku deklarowania typów metadanych:

* Udostępnij je jako [atrybuty](/dotnet/csharp/programming-guide/concepts/attributes/).
* Większość użytkowników zna zastosowanie atrybutów.

Deklarowanie typu metadanych jako interfejsu dodaje kolejną warstwę elastyczność:

* Interfejsy są umożliwiające tworzenie.
* Deweloperzy mogą zadeklarować własne typy, które łączą wiele zasad.

**Czy** można zastąpić metadane, jak pokazano w następującym przykładzie:

[!code-csharp[](routing/samples/3.x/RoutingSample/ICoolMetadata.cs?name=snippet)]

Najlepszym sposobem postępowania z tymi wskazówkami jest uniknięcie definiowania **metadanych znacznika**:

* Nie wyszukuj tylko obecności typu metadanych.
* Zdefiniuj Właściwość metadanych i sprawdź właściwość.

Zbieranie metadanych jest uporządkowane i obsługuje zastępowanie według priorytetu. W przypadku kontrolerów metadane metody akcji są najbardziej specyficzne.

**Zrób** oprogramowanie pośredniczące przydatne w przypadku routingu i bez niego.

```csharp
app.UseRouting();

app.UseAuthorization(new AuthorizationPolicy() { ... });

app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...).RequrireAuthorization();
});
```

Na przykład w tych wytycznych należy wziąć pod uwagę `UseAuthorization` oprogramowanie pośredniczące. Oprogramowanie pośredniczące autoryzacji umożliwia przekazywanie zasad powrotu. <!-- shown where?  (shown here) --> Zasady powrotu, jeśli są określone, mają zastosowanie do obu:

* Punkty końcowe bez określonych zasad.
* Żądania, które nie pasują do punktu końcowego.

Dzięki temu oprogramowanie pośredniczące autoryzacji jest przydatne poza kontekstem routingu. Oprogramowanie pośredniczące autoryzacji może być używane na potrzeby tradycyjnego programowania oprogramowania pośredniczącego.

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Routing jest odpowiedzialny za mapowanie identyfikatorów URI żądań na punkty końcowe i wysyłanie żądań przychodzących do tych punktów końcowych. Trasy są zdefiniowane w aplikacji i konfigurowane podczas uruchamiania aplikacji. Trasa może opcjonalnie wyodrębnić wartości z adresu URL zawartego w żądaniu. te wartości mogą być następnie używane do przetwarzania żądań. Przy użyciu informacji o trasie z aplikacji Routing jest również w stanie generować adresy URL mapowane na punkty końcowe.

Aby użyć najnowszych scenariuszy routingu w ASP.NET Core 2,2, określ [wersję zgodności](xref:mvc/compatibility-version) do rejestracji usług MVC w `Startup.ConfigureServices` :

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<xref:Microsoft.AspNetCore.Mvc.MvcOptions.EnableEndpointRouting>Opcja określa, czy funkcja routingu powinna wewnętrznie używać logiki opartej na punkcie końcowym lub <xref:Microsoft.AspNetCore.Routing.IRouter> logiki opartej na ASP.NET Core 2,1 lub starszej. Gdy wersja zgodności jest ustawiona na 2,2 lub nowszej, wartość domyślna to `true` . Ustaw wartość na, aby `false` użyć poprzedniej logiki routingu:

```csharp
// Use the routing logic of ASP.NET Core 2.1 or earlier:
services.AddMvc(options => options.EnableEndpointRouting = false)
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

Więcej informacji na temat <xref:Microsoft.AspNetCore.Routing.IRouter> routingu opartego na usłudze można znaleźć w [wersji ASP.NET Core 2,1 tego tematu](/aspnet/core/fundamentals/routing?view=aspnetcore-2.1).

> [!IMPORTANT]
> Ten dokument obejmuje Routing ASP.NET Core niskiego poziomu. Aby uzyskać informacje na temat ASP.NET Core routingu MVC, zobacz <xref:mvc/controllers/routing> . Aby uzyskać informacje na temat Konwencji routingu na Razor stronach, zobacz <xref:razor-pages/razor-pages-conventions> .

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="routing-basics"></a>Podstawy routingu

Większość aplikacji powinna wybrać podstawowy i opisowy schemat routingu, aby adresy URL były czytelne i zrozumiałe. Domyślna trasa konwencjonalna `{controller=Home}/{action=Index}/{id?}` :

* Obsługuje podstawowy i opisowy schemat routingu.
* Jest użytecznym punktem wyjścia dla aplikacji opartych na interfejsie użytkownika.

Deweloperzy często dodają dodatkowe trasy zwięzła do obszarów o dużym ruchu w aplikacji w wyspecjalizowanych sytuacjach przy użyciu [routingu atrybutów](xref:mvc/controllers/routing#attribute-routing) lub dedykowanych tras konwencjonalnych. Wyspecjalizowane sytuacje obejmują m.in. punkty końcowe, blog i handlu elektronicznego.

Interfejsy API sieci Web powinny używać routingu atrybutów do modelowania funkcjonalności aplikacji jako zestawu zasobów, w których operacje są reprezentowane przez zlecenia HTTP. Oznacza to, że wiele operacji, na przykład GET i POST, dla tego samego zasobu logicznego używa tego samego adresu URL. Routing atrybutu zapewnia poziom kontroli, który jest wymagany do dokładnego projektowania układu publicznego punktu końcowego interfejsu API.

RazorAplikacje stron używają domyślnego routingu konwencjonalnego do obsłużenia nazwanych zasobów w folderze *strony* aplikacji. Dostępne są dodatkowe konwencje umożliwiające dostosowywanie Razor zachowania routingu stron. Aby uzyskać więcej informacji, zobacz <xref:razor-pages/index> i <xref:razor-pages/razor-pages-conventions>.

Obsługa generowania adresów URL umożliwia tworzenie aplikacji bez adresów URL, które mają być połączone ze sobą. Ta obsługa pozwala rozpocząć od podstawowej konfiguracji routingu i zmodyfikować trasy po ustaleniu układu zasobów aplikacji.

Routing używa *punktów końcowych* ( `Endpoint` ) do reprezentowania logicznych punktów końcowych w aplikacji.

Punkt końcowy definiuje delegata do przetwarzania żądań i kolekcji dowolnych metadanych. Metadane są używane w celu zaimplementowania zagadnień związanych z wycinaniem w oparciu o zasady i konfigurację dołączone do każdego punktu końcowego.

System routingu ma następujące cechy:

* Składnia szablonu trasy służy do definiowania tras z parametrami trasy z tokenami.
* Dozwolona jest konfiguracja języka końcowego w stylu konwencjonalnym i stylu atrybutu.
* <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>służy do określenia, czy parametr adresu URL zawiera prawidłową wartość dla danego ograniczenia punktu końcowego.
* Modele aplikacji, takie jak MVC/ Razor Pages, rejestrują wszystkie punkty końcowe, które mają przewidywalne implementację scenariuszy routingu.
* Implementacja routingu podejmuje decyzje dotyczące routingu w dowolnym miejscu w potoku programu pośredniczącego.
* Oprogramowanie pośredniczące, które pojawia się po utworzeniu oprogramowania pośredniczącego, może sprawdzić wynik decyzji punktu końcowego usługi routingu dla danego identyfikatora URI żądania.
* Można wyliczyć wszystkie punkty końcowe w aplikacji w dowolnym miejscu w potoku programu pośredniczącego.
* Aplikacja może używać routingu do generowania adresów URL (na przykład w przypadku przekierowania lub linków) na podstawie informacji o punkcie końcowym, a tym samym unikania zakodowanych adresów URL, co ułatwia łatwość utrzymania.
* Generowanie adresów URL jest oparte na adresach, które obsługują arbitralną rozszerzalność:

  * Interfejs API generatora linków ( <xref:Microsoft.AspNetCore.Routing.LinkGenerator> ) można rozpoznać w dowolnym miejscu przy użyciu [iniekcji zależności (di)](xref:fundamentals/dependency-injection) do generowania adresów URL.
  * Gdy interfejs API generatora linków nie jest dostępny za pośrednictwem programu DI, <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> oferuje metody tworzenia adresów URL.

> [!NOTE]
> Dzięki wydaniu routingu punktów końcowych w ASP.NET Core 2,2, łączenie punktów końcowych jest ograniczone do Razor akcji i stron MVC/Pages. Rozszerzenia punktów końcowych konsolidacji są planowane dla przyszłych wersji.

Routing jest połączony z potokiem [pośredniczącym](xref:fundamentals/middleware/index) przez <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> klasę. [ASP.NET Core MVC](xref:mvc/overview) dodaje Routing do potoku oprogramowania pośredniczącego w ramach swojej konfiguracji i obsługuje routing w aplikacjach MVC i Razor Pages. Aby dowiedzieć się, jak używać routingu jako składnika autonomicznego, zapoznaj się z sekcją [Korzystanie z oprogramowania do routingu](#use-routing-middleware) .

### <a name="url-matching"></a>Dopasowanie adresu URL

Dopasowywanie adresów URL to proces, za pomocą którego Routing wysyła żądanie przychodzące do *punktu końcowego*. Ten proces jest oparty na danych w ścieżce URL, ale można go rozszerzyć w celu uwzględnienia wszelkich danych w żądaniu. Możliwość wysyłania żądań do oddzielnych programów obsługi jest kluczem do skalowania rozmiaru i złożoności aplikacji.

System routingu w ramach routingu punktów końcowych jest odpowiedzialny za wszystkie decyzje dotyczące wysyłania. Ponieważ oprogramowanie pośredniczące stosuje zasady oparte na wybranym punkcie końcowym, ważne jest, aby wszystkie decyzje, które mogą mieć wpływ na wysyłanie lub stosowanie zasad zabezpieczeń, zostały wprowadzone w systemie routingu.

Po wykonaniu delegata punktu końcowego właściwości [RouteContext. RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) są ustawiane na odpowiednie wartości na podstawie przetwarzania żądania wykonanego do tej pory.

[RouteData. Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) to słownik *wartości tras* uzyskanych z trasy. Te wartości są zwykle określane przez tokenizowanie jako adres URL i mogą służyć do akceptowania danych wejściowych użytkownika lub do dalszej akceptacji decyzji w aplikacji.

[RouteData. DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) to zbiór właściwości dodatkowych danych dotyczących dopasowanej trasy. <xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*>zapewnia obsługę kojarzenia danych stanu z każdą trasą, dzięki czemu aplikacja może podejmować decyzje na podstawie dopasowanej trasy. Te wartości są zdefiniowane przez dewelopera i **nie** mają wpływu na zachowanie routingu. Ponadto wartości umieszczane w [RouteData. Datatokeny](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) mogą być dowolnego typu, w przeciwieństwie do [RouteData. wartości](xref:Microsoft.AspNetCore.Routing.RouteData.Values), które muszą być konwertowane do i z ciągów.

[RouteData. routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) to lista tras, które brały udział w pomyślnie dopasowane do żądania. Trasy mogą być zagnieżdżone wewnątrz siebie. <xref:Microsoft.AspNetCore.Routing.RouteData.Routers>Właściwość odzwierciedla ścieżkę przez logiczne drzewo tras, które spowodowały dopasowanie. Ogólnie rzecz biorąc, pierwszy element w <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> jest kolekcją tras i powinien być używany do generowania adresów URL. Ostatnim elementem w programie <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> jest program obsługi trasy, który został dopasowany.

<a name="lg"></a>

### <a name="url-generation-with-linkgenerator"></a>Generowanie adresu URL za pomocą LinkGenerator

Generowanie adresu URL to proces, za pomocą którego Routing może utworzyć ścieżkę URL na podstawie zestawu wartości trasy. Pozwala to na logiczne rozdzielenie między punktami końcowymi i adresami URL, które uzyskują do nich dostęp.

Routing punktów końcowych obejmuje interfejs API generatora linków ( <xref:Microsoft.AspNetCore.Routing.LinkGenerator> ). <xref:Microsoft.AspNetCore.Routing.LinkGenerator>jest usługą singleton, którą można pobrać z programu [di](xref:fundamentals/dependency-injection). Interfejsu API można używać poza kontekstem żądania wykonania. MVC <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> i scenariusze, które opierają <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> się na, takie jak [pomocnicy tagów](xref:mvc/views/tag-helpers/intro), pomocników HTML i [wyniki akcji](xref:mvc/controllers/actions), używają generatora linków, aby zapewnić możliwości generowania linków.

Generator łącza jest objęty koncepcją i *schematami* *adresów.* Schemat adresów jest sposobem określania punktów końcowych, które należy wziąć pod uwagę podczas generowania łącza. Na przykład nazwa trasy i wartości trasy scenariusze wielu użytkowników są znane ze względu na to, że z MVC/ Razor Pages są implementowane jako schemat adresów.

Generator łącza umożliwia łączenie z Razor akcjami i stronami MVC/Pages za pomocą następujących metod rozszerzających:

* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*>
* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetPathByPage*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetUriByPage*>

Przeciążenie tych metod przyjmuje argumenty, które zawierają `HttpContext` . Metody te są funkcjonalnie równoważne z `Url.Action` i `Url.Page` oferują dodatkową elastyczność i opcje.

`GetPath*`Metody są najbardziej podobne do `Url.Action` i `Url.Page` w tym, że generują identyfikator URI zawierający ścieżkę bezwzględną. `GetUri*`Metody zawsze generują bezwzględny identyfikator URI zawierający schemat i hosta. Metody, które akceptują `HttpContext` Identyfikator URI w kontekście żądania wykonania. Użycie wartości tras w otoczeniu, ścieżki podstawowej adresu URL, schematu i hosta z żądania wykonania jest używane, chyba że zostaną zastąpione.

<xref:Microsoft.AspNetCore.Routing.LinkGenerator>jest wywoływana przy użyciu adresu. Generowanie identyfikatora URI występuje w dwóch krokach:

1. Adres jest powiązany z listą punktów końcowych, które pasują do adresu.
1. Wszystkie punkty końcowe `RoutePattern` są oceniane do momentu znalezienia wzorca tras pasującego do dostarczonych wartości. Wynikowe dane wyjściowe są łączone z innymi częściami identyfikatora URI dostarczanymi do generatora linków i zwracanymi.

Metody zapewniane przez <xref:Microsoft.AspNetCore.Routing.LinkGenerator> obsługę funkcji generowania linków standardowych dla dowolnego typu adresu. Najbardziej wygodnym sposobem korzystania z generatora łączy są metody rozszerzające, które wykonują operacje dla określonego typu adresu.

| Metoda rozszerzenia   | Opis                                                         |
| ---
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

--------- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

---------------------------------- | | <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | Generuje identyfikator URI z ścieżką bezwzględną na podstawie podanych wartości. | | <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | Generuje bezwzględny identyfikator URI na podstawie podanych wartości.             |

> [!WARNING]
> Należy zwrócić uwagę na następujące konsekwencje wywoływania <xref:Microsoft.AspNetCore.Routing.LinkGenerator> metod:
>
> * Użyj `GetUri*` metod rozszerzających z zachowaniem ostrożności w konfiguracji aplikacji, która nie weryfikuje `Host` nagłówka żądań przychodzących. Jeśli `Host` nagłówek żądań przychodzących nie jest zweryfikowany, dane wejściowe żądania niezaufanego mogą być wysyłane z powrotem do klienta w identyfikatorach URI w widoku/stronie. Zaleca się, aby wszystkie aplikacje produkcyjne skonfigurowali swój serwer do sprawdzania poprawności `Host` nagłówka pod kątem znanych prawidłowych wartości.
>
> * Należy używać <xref:Microsoft.AspNetCore.Routing.LinkGenerator> z zachowaniem ostrożności w oprogramowaniu pośredniczącym w połączeniu z `Map` lub `MapWhen` . `Map*`zmienia ścieżkę podstawową żądania wykonania, która ma wpływ na dane wyjściowe generowania łącza. Wszystkie <xref:Microsoft.AspNetCore.Routing.LinkGenerator> interfejsy API umożliwiają określanie ścieżki podstawowej. Zawsze określaj pustą ścieżkę bazową, która ma `Map*` wpływ na generowanie linków.

## <a name="differences-from-earlier-versions-of-routing"></a>Różnice wynikające z wcześniejszych wersji usługi Routing

Istnieje kilka różnic między routingiem punktu końcowego w ASP.NET Core 2,2 lub nowszym i wcześniejszymi wersjami routingu w programie ASP.NET Core:

* System routingu punktu końcowego nie obsługuje <xref:Microsoft.AspNetCore.Routing.IRouter> rozszerzalności opartej na systemie, w tym dziedziczenie z <xref:Microsoft.AspNetCore.Routing.Route> .

* Routing punktów końcowych nie obsługuje [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim). Użyj [wersji zgodności](xref:mvc/compatibility-version) 2,1 ( `.SetCompatibilityVersion(CompatibilityVersion.Version_2_1)` ), aby nadal korzystać z podkładki zgodności.

* Routing punktów końcowych ma inne zachowanie dla wielkości liter wygenerowanych identyfikatorów URI podczas korzystania z konwencjonalnych tras.

  Rozważmy następujący szablon trasy domyślnej:

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  Załóżmy, że generujesz link do akcji przy użyciu następującej trasy:

  ```csharp
  var link = Url.Action("ReadPost", "blog", new { id = 17, });
  ```

  W przypadku <xref:Microsoft.AspNetCore.Routing.IRouter> routingu opartego na systemie ten kod generuje identyfikator URI `/blog/ReadPost/17` , który uwzględnia wielkość liter podanej trasy. Routing punktów końcowych w ASP.NET Core 2,2 lub późniejszych produkuje `/Blog/ReadPost/17` ("blog" jest pisane wielkimi literami). Routing punktów końcowych udostępnia `IOutboundParameterTransformer` interfejs, którego można użyć do dostosowania tego zachowania globalnie lub w celu zastosowania różnych konwencji do mapowania adresów URL.

  Aby uzyskać więcej informacji, zobacz sekcję [informacje dotyczące transformatora parametrów](#parameter-transformer-reference) .

* Generowanie linków używane przez strony MVC/ Razor Pages z trasami konwencjonalnymi działa inaczej podczas próby połączenia z kontrolerem/akcją lub stroną, która nie istnieje.

  Rozważmy następujący szablon trasy domyślnej:

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  Załóżmy, że generowany jest link do akcji przy użyciu szablonu domyślnego z następującymi:

  ```csharp
  var link = Url.Action("ReadPost", "Blog", new { id = 17, });
  ```

  W przypadku `IRouter` routingu opartego na systemie wynik jest zawsze `/Blog/ReadPost/17` , nawet jeśli `BlogController` nie istnieje lub nie ma `ReadPost` metody akcji. Zgodnie z oczekiwaniami Routing punktów końcowych w ASP.NET Core 2,2 lub nowszej generuje, `/Blog/ReadPost/17` czy istnieje metoda akcji. *Jednak w przypadku, gdy akcja nie istnieje, routing punktu końcowego tworzy pusty ciąg.* Koncepcyjnie, routing punktu końcowego nie zakłada, że punkt końcowy istnieje, jeśli akcja nie istnieje.

* *Algorytm unieważniania wartości otoczenia* generacji linku działa inaczej, gdy jest używany z routingiem punktów końcowych.

  *Nieprawidłowa wartość otoczenia* , która decyduje o tym, które wartości trasy z aktualnie wykonywanego żądania (wartości otoczenia) mogą być używane w operacjach generowania linków. Routowanie konwencjonalne zawsze unieważnia dodatkowe wartości trasy podczas łączenia z inną akcją. Routing atrybutów nie miał tego zachowania przed wydaniem ASP.NET Core 2,2. We wcześniejszych wersjach ASP.NET Core linki do innej akcji, która używa tych samych nazw parametrów trasy, spowodowały błędy generacji łącza. W ASP.NET Core 2,2 lub nowszej, obie formy routingu unieważnią wartości podczas łączenia z inną akcją.

  Rozważmy następujący przykład w ASP.NET Core 2,1 lub starszej. W przypadku łączenia z inną akcją (lub inną stroną) wartości trasy mogą być ponownie używane na różne sposoby.

  W */Pages/Store/Product.cshtml*:

  ```cshtml
  @page "{id}"
  @Url.Page("/Login")
  ```

  W */Pages/login.cshtml*:

  ```cshtml
  @page "{id?}"
  ```

  Jeśli identyfikator URI znajduje się `/Store/Product/18` w ASP.NET Core 2,1 lub starszej, link wygenerowany na stronie Sklep/informacje w usłudze `@Url.Page("/Login")` ma wartość `/Login/18` . `id`Wartość 18 jest ponownie używana, mimo że miejsce docelowe linku jest częścią aplikacji. `id`Wartość trasy w kontekście `/Login` strony jest prawdopodobnie wartością identyfikatora użytkownika, a nie wartością identyfikatora produktu w sklepie.

  W przypadku routingu punktów końcowych z ASP.NET Core 2,2 lub nowszym wynik jest `/Login` . Wartości otoczenia nie są ponownie używane, gdy połączone miejsce docelowe jest inną akcją lub stroną.

* Składnia parametru trasy okrężnej: ukośniki nie są kodowane przy użyciu podwójnej gwiazdki ( `**` ) składni parametrów.

  Podczas generowania łącza system routingu koduje wartość przechwyconą w dwugwiazdkowym ( `**` ) parametrem catch-all (na przykład), `{**myparametername}` z wyjątkiem ukośników. Podwójna gwiazdka catch-all jest obsługiwana w przypadku `IRouter` routingu opartego na systemie ASP.NET Core 2,2 lub nowszym.

  Pojedyncza gwiazdka "catch-all" w poprzednich wersjach ASP.NET Core ( `{*myparametername}` ) pozostanie obsługiwana, a ukośniki są zakodowane.

  | Trasa              | Wygenerowano łącze<br>`Url.Action(new { category = "admin/products" })`&hellip; |
  | ---
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

--------- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

----------------------------------- |   | `/search/{*page}`| `/search/admin%2Fproducts`(ukośnik zostanie zakodowany) |   | `/search/{**page}` |  `/search/admin/products`                                              |

### <a name="middleware-example"></a>Przykład oprogramowania pośredniczącego

W poniższym przykładzie oprogramowanie pośredniczące używa <xref:Microsoft.AspNetCore.Routing.LinkGenerator> interfejsu API do tworzenia linku do metody akcji, która wyświetla listę produktów ze sklepu. Przy użyciu generatora linków poprzez wstrzyknięcie go do klasy i wywołanie `GenerateLink` jest dostępne dla dowolnej klasy w aplikacji.

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

Większość aplikacji tworzy trasy przez wywołanie <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> lub jedną z podobnych metod rozszerzających zdefiniowanych w systemie <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> . Dowolna z <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> metod rozszerzających tworzy wystąpienie <xref:Microsoft.AspNetCore.Routing.Route> i dodaje je do kolekcji tras.

<xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>nie akceptuje parametru procedury obsługi trasy. <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>dodaje tylko trasy, które są obsługiwane przez <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*> . Aby dowiedzieć się więcej na temat routingu w MVC, zobacz <xref:mvc/controllers/routing> .

Poniższy przykład kodu jest przykładem <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> wywołania używanego przez typową definicję trasy MVC ASP.NET Core:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Ten szablon dopasowuje ścieżkę URL i wyodrębnia wartości tras. Na przykład ścieżka `/Products/Details/17` generuje następujące wartości trasy: `{ controller = Products, action = Details, id = 17 }` .

Wartości tras są określane przez podzielenie ścieżki URL na segmenty i dopasowanie do każdego segmentu przy użyciu nazwy *parametru trasy* w szablonie trasy. Parametry trasy są nazywane. Parametry zdefiniowane przez ujęcie nazwy parametru w nawiasach klamrowych `{ ... }` .

Poprzedni szablon może również pasować do ścieżki URL `/` i generować wartości `{ controller = Home, action = Index }` . Dzieje się tak, `{controller}` ponieważ `{action}` Parametry i trasy mają wartości domyślne, a `id` parametr trasy jest opcjonalny. Znak równości ( `=` ), po którym następuje wartość po nazwie parametru trasy definiuje wartość domyślną dla parametru. Znak zapytania ( `?` ) po nazwie parametru trasy definiuje opcjonalny parametr.

Parametry trasy z wartością domyślną *zawsze* generują wartość trasy w przypadku dopasowania trasy. Parametry opcjonalne nie generują wartości trasy, jeśli nie ma odpowiedniego segmentu ścieżki adresu URL. Szczegółowe opisy scenariuszy i składni szablonów tras można znaleźć w sekcji [Dokumentacja dotycząca szablonu trasy](#route-template-reference) .

W poniższym przykładzie definicja parametru trasy `{id:int}` definiuje [ograniczenie trasy](#route-constraint-reference) dla `id` parametru trasy:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

Ten szablon dopasowuje ścieżkę URL, taką jak, `/Products/Details/17` ale nie `/Products/Details/Apples` . Ograniczenia trasy implementują <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> i sprawdzają wartości routingu w celu ich zweryfikowania. W tym przykładzie wartość trasy `id` musi być możliwa do przekonwertowania na liczbę całkowitą. Aby uzyskać wyjaśnienie ograniczeń trasy dostarczonych przez platformę, zobacz temat [ograniczenia trasy](#route-constraint-reference) .

Dodatkowe przeciążenia <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> wartości akceptują dla `constraints` , `dataTokens` i `defaults` . Typowym użyciem tych parametrów jest przekazanie anonimowo wpisanego obiektu, gdzie nazwy właściwości typu anonimowego są zgodne z nazwami parametrów trasy.

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
> Wbudowana Składnia służąca do definiowania ograniczeń i wartości domyślnych może być wygodna dla prostych tras. Istnieją jednak scenariusze, takie jak tokeny danych, które nie są obsługiwane przez składnię wbudowaną.

Poniższy przykład ilustruje kilka dodatkowych scenariuszy:

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{**article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

Poprzedni szablon pasuje do ścieżki URL, tak jak `/Blog/All-About-Routing/Introduction` i wyodrębnia wartości `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }` . Domyślne wartości trasy dla `controller` i `action` są generowane przez trasę, nawet jeśli w szablonie nie ma odpowiednich parametrów trasy. Wartości domyślne można określić w szablonie trasy. `article`Parametr Route jest zdefiniowany jako *przechwycenie* przez wygląd podwójnej gwiazdki ( `**` ) przed nazwą parametru trasy. Catch-wszystkie parametry tras przechwytują resztę ścieżki URL i mogą również pasować do pustego ciągu.

Poniższy przykład dodaje ograniczenia trasy i tokeny danych:

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

Poprzedni szablon pasuje do ścieżki URL, na przykład `/en-US/Products/5` i wyodrębnia wartości `{ controller = Products, action = Details, id = 5 }` i tokeny danych `{ locale = en-US }` .

![Lokalne tokeny systemu Windows](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a>Generowanie adresu URL klasy trasy

<xref:Microsoft.AspNetCore.Routing.Route>Klasa może również wykonywać generowanie adresów URL przez połączenie zestawu wartości tras z jego szablonem trasy. Jest to logicznie proces odwrotny pasujący do ścieżki URL.

> [!TIP]
> Aby lepiej zrozumieć generowanie adresów URL, Załóżmy, jaki adres URL ma zostać wygenerowany, a następnie pomyśl o sposobie dopasowania szablonu trasy do tego adresu URL. Jakie wartości zostałyby wygenerowane? Jest to sztywny odpowiednik działania generowania adresów URL w <xref:Microsoft.AspNetCore.Routing.Route> klasie.

Poniższy przykład używa ogólnej trasy domyślnej ASP.NET Core MVC:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

W przypadku wartości trasy `{ controller = Products, action = List }` `/Products/List` zostanie wygenerowany adres URL. Wartości trasy są zastępowane odpowiednimi parametrami trasy w celu utworzenia ścieżki URL. Ponieważ `id` jest to opcjonalny parametr trasy, adres URL został pomyślnie wygenerowany bez wartości dla `id` .

W przypadku wartości trasy `{ controller = Home, action = Index }` `/` zostanie wygenerowany adres URL. Podane wartości trasy są zgodne z wartościami domyślnymi, a segmenty odpowiadające wartościom domyślnym są bezpiecznie pomijane.

Oba adresy URL wygenerowały rundę z następującą definicją trasy ( `/Home/Index` i `/` ) tworzą te same wartości trasy, które zostały użyte do WYGENEROWANIA adresu URL.

> [!NOTE]
> Aplikacja używająca ASP.NET Core MVC powinna używać <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> do generowania adresów URL zamiast bezpośredniego wywoływania routingu.

Aby uzyskać więcej informacji na temat generowania adresów URL, zobacz sekcję [informacje dotyczące generowania adresów URL](#url-generation-reference) .

## <a name="use-routing-middleware"></a>Korzystanie z oprogramowania pośredniczącego routingu

Odwołuje się do [pakietu Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) w pliku projektu aplikacji.

Dodaj Routing do kontenera usługi w `Startup.ConfigureServices` :

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

Trasy muszą być skonfigurowane w `Startup.Configure` metodzie. Przykładowa aplikacja używa następujących interfejsów API:

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>: Dopasowuje tylko żądania HTTP GET.
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

W poniższej tabeli przedstawiono odpowiedzi z podanym identyfikatorem URI.

| Identyfikator URI                    | Odpowiedź                                          |
| ---
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

----------- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

------------------------- | | `/package/create/3`    | Cześć! Wartości trasy: [Operation, Create], [ID, 3] | | `/package/track/-3`    | Cześć! Wartości trasy: [Operation, Track], [ID,-3] | | `/package/track/-3/`   | Cześć! Wartości trasy: [Operation, Track], [ID,-3] | | `/package/track/`      | Żądanie przepada w, brak dopasowania.              | | `GET /hello/Joe`       | Witaj, Jan!                                          | | `POST /hello/Joe`      | Żądanie przepada w, dopasowuje tylko HTTP GET. | | `GET /hello/Joe/Smith` | Żądanie przepada w, brak dopasowania.              |

Struktura zawiera zestaw metod rozszerzających do tworzenia tras ( <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions> ):

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

`Map[Verb]`Metody służą do ograniczania trasy do zlecenia HTTP w nazwie metody. Na przykład zobacz <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> i <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*> .

## <a name="route-template-reference"></a>Odwołanie do szablonu trasy

Tokeny w nawiasach klamrowych ( `{ ... }` ) definiują *Parametry trasy* , które są powiązane w przypadku dopasowania trasy. Można zdefiniować więcej niż jeden parametr trasy w segmencie trasy, ale muszą one być oddzielone wartością literału. Na przykład `{controller=Home}{action=Index}` nie jest prawidłową trasą, ponieważ nie ma wartości literału między `{controller}` i `{action}` . Te parametry trasy muszą mieć nazwę i mogą mieć określone dodatkowe atrybuty.

Tekst literału inny niż parametry trasy (na przykład `{id}` ) i separator ścieżki `/` muszą być zgodne z tekstem w adresie URL. W dopasowaniu do tekstu nie jest rozróżniana wielkość liter i w oparciu o zdekodowaną reprezentację ścieżki URL. Aby dopasować Ogranicznik parametru trasy literału ( `{` lub `}` ), należy wprowadzić ogranicznik przez powtórzenie znaku ( `{{` lub `}}` ).

Wzorce adresów URL, które próbują przechwycić nazwę pliku z opcjonalnym rozszerzeniem pliku, mają dodatkowe uwagi. Rozważmy na przykład szablon `files/{filename}.{ext?}` . Gdy wartości obu `filename` i `ext` istnieją, są wypełniane obie wartości. Jeśli `filename` w adresie URL znajduje się tylko wartość parametru, trasa pasuje do, ponieważ końcowy okres ( `.` ) jest opcjonalny. Następujące adresy URL pasują do tej trasy:

* `/files/myFile.txt`
* `/files/myFile`

Możesz użyć gwiazdki ( `*` ) lub podwójnej gwiazdki ( `**` ) jako prefiksu do parametru trasy, aby powiązać z pozostałą częścią identyfikatora URI. Są one nazywane parametrami *przechwycenia* . Na przykład `blog/{**slug}` dopasowuje dowolny identyfikator URI, który rozpoczyna się od `/blog` i ma dowolną wartość, która jest przypisana do `slug` wartości trasy. Catch-wszystkie parametry można także dopasować do pustego ciągu.

Parametr catch-all wyprowadza odpowiednie znaki, gdy trasa jest używana do generowania adresu URL, włącznie z separatorami ścieżki ( `/` ). Na przykład trasa `foo/{*path}` z wartościami trasy jest `{ path = "my/path" }` generowana `foo/my%2Fpath` . Zwróć uwagę na odwrócony ukośnik. Aby zaokrąglić znaki separatora ścieżki, użyj `**` prefiksu parametru trasy. Trasa `foo/{**path}` z `{ path = "my/path" }` generowaniem `foo/my/path` .

Parametry trasy mogą mieć *wartości domyślne* , określając wartość domyślną po nazwie parametru oddzielone znakiem równości ( `=` ). Na przykład `{controller=Home}` definiuje `Home` jako wartość domyślną dla `controller` . Wartość domyślna jest używana, jeśli żadna wartość nie jest obecna w adresie URL dla parametru. Parametry trasy są opcjonalne przez dołączenie znaku zapytania ( `?` ) na końcu nazwy parametru, jak w `id?` . Różnica między wartościami opcjonalnymi i domyślnymi parametrami trasy polega na tym, że parametr trasy z wartością domyślną zawsze tworzy wartość, &mdash; a opcjonalny parametr ma wartość tylko wtedy, gdy wartość jest dostarczana przez adres URL żądania.

Parametry trasy mogą mieć ograniczenia, które muszą być zgodne z wartością trasy powiązaną z adresem URL. Dodanie dwukropka ( `:` ) i nazwy ograniczenia po nazwie parametru trasy określa *ograniczenie wbudowane* dla parametru trasy. Jeśli ograniczenie wymaga argumentów, są one ujęte w nawiasy ( `(...)` ) po nazwie ograniczenia. Można określić wiele ograniczeń wbudowanych, dołączając inną dwukropek ( `:` ) i nazwę ograniczenia.

Nazwa i argumenty ograniczenia są przekazane do usługi w <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> celu utworzenia wystąpienia <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> do użycia w przetwarzaniu adresów URL. Na przykład szablon trasy `blog/{article:minlength(10)}` określa `minlength` ograniczenie z argumentem `10` . Aby uzyskać więcej informacji na temat ograniczeń trasy i listę ograniczeń zapewnianych przez platformę, zobacz sekcję [odwołanie do ograniczenia trasy](#route-constraint-reference) .

Parametry trasy mogą także mieć Transformatory parametrów, które przekształcają wartość parametru podczas generowania linków i dopasowywania stron do adresów URL. Podobnie jak ograniczenia, transformatory parametrów mogą być dodawane wewnętrznie do parametru trasy przez dodanie dwukropka ( `:` ) i nazwy transformatora po nazwie parametru trasy. Na przykład szablon trasy `blog/{article:slugify}` określa `slugify` transformator. Aby uzyskać więcej informacji na temat transformatorów parametrów, zobacz sekcję [informacje dotyczące transformatora parametrów](#parameter-transformer-reference) .

W poniższej tabeli przedstawiono przykładowe szablony tras i ich zachowanie.

| Szablon trasy                           | Przykładowy pasujący identyfikator URI    | Identyfikator URI żądania&hellip;                                                    |
| ---
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-------------------- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

------------ | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

------------------------------------- | | `hello`                                  | `/hello`                | Dopasowuje tylko jedną ścieżkę `/hello` .                                     | | `{Page=Home}`                            | `/`                     | Dopasowuje i ustawia `Page` jako `Home` .                                         | | `{Page=Home}`                            | `/Contact`              | Dopasowuje i ustawia `Page` jako `Contact` .                                      | | `{controller}/{action}/{id?}`            | `/Products/List`        | Mapuje do `Products` kontrolera i `List` akcji.                       | | `{controller}/{action}/{id?}`            | `/Products/Details/123` | Mapuje do `Products` kontrolera i `Details` akcji ( `id` ustawienie na 123). | | `{controller=Home}/{action=Index}/{id?}` | `/`                     | Mapuje na `Home` kontroler i `Index` metodę ( `id` jest ignorowana).        |

Użycie szablonu jest ogólnie najprostszym podejściem do routingu. Ograniczenia i wartości domyślne można także określić poza szablonem trasy.

> [!TIP]
> Włącz [Rejestrowanie](xref:fundamentals/logging/index) , aby zobaczyć, jak wbudowane implementacje routingu, takie jak <xref:Microsoft.AspNetCore.Routing.Route> , dopasowują żądania.

## <a name="reserved-routing-names"></a>Zastrzeżone nazwy routingu

Następujące słowa kluczowe są nazwami zarezerwowanymi i nie mogą być używane jako nazwy tras lub parametry:

* `action`
* `area`
* `controller`
* `handler`
* `page`

## <a name="route-constraint-reference"></a>Odwołanie do ograniczenia trasy

Ograniczenia trasy są wykonywane, gdy nastąpiło dopasowanie do przychodzącego adresu URL, a Ścieżka adresu URL jest zgodna z wartościami trasy. Ograniczenia trasy zwykle sprawdzają wartość trasy skojarzoną za pośrednictwem szablonu trasy i podejmują decyzję o tym, czy wartość jest akceptowalna. Niektóre ograniczenia trasy używają danych poza wartością trasy, aby uwzględnić, czy żądanie może być kierowane. Na przykład <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> może akceptować lub odrzucać żądanie na podstawie jego zlecenia http. Ograniczenia są używane w żądaniach routingu i generowania linków.

> [!WARNING]
> Nie używaj ograniczeń **sprawdzania poprawności danych wejściowych**. Jeśli ograniczenia są używane do **sprawdzania poprawności danych wejściowych**, nieprawidłowe dane wejściowe w odpowiedzi *404 — nie znaleziono* , zamiast *żądania 400-złe* z odpowiednim komunikatem o błędzie. Ograniczenia trasy są **używane do** odróżniania podobnych tras, a nie do sprawdzania danych wejściowych dla określonej trasy.

W poniższej tabeli przedstawiono przykładowe ograniczenia trasy i ich oczekiwane zachowanie.

| ograniczenie | Przykład | Przykładowe dopasowania | Uwagi |
| ---
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

----- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

---- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-------- | ----- | | `int` | `{id:int}` | `123456789`, `-123456789` | Dopasowuje dowolną liczbę całkowitą. | | `bool` | `{active:bool}` | `true`, `FALSE` | Dopasowuje `true` lub `false. Case-insensitive. |
| ` DateTime ` | ` {dob: DateTime} ` | ` 2016-12-31 `, ` 2016-12-31 7:32pm ` | Matches a valid ` DateTime ` value in the invariant culture. See  preceding warning.|
| ` Decimal ` | ` {Price: decimal} ` | ` 49,99 `, ` -1 000,01 ` | Matches a valid ` dziesiętne ` value in the invariant culture. See  preceding warning.|
| ` Double ` | ` {Waga: Double} ` | ` 1,234 `, ` -1, 001.01 E8 ` | Matches a valid ` podwójne ` value in the invariant culture. See  preceding warning.|
| ` zmiennoprzecinkowe ` | ` {Waga: float} ` | ` 1,234 `, ` -1, 001.01 E8 ` | Matches a valid ` float ` value in the invariant culture. See  preceding warning.|
| ` GUID ` | ` {ID: GUID} ` | ` CD2C1638-1638-72D5-1638-DEADBEEF1638 `, ` {CD2C1638-1638-72D5-1638-DEADBEEF1638} ` | Matches a valid ` GUID ` value. |
| ` Long ` | ` {Ticks: Long} ` | ` 123456789 `, ` -123456789 ` | Matches a valid ` Long ` value. |
| ` minLength (Value) ` | ` {username: minLength (4)} ` | ` Rick ` | String must be at least 4 characters. |
| ` MaxLength (Value) ` | ` {filename: MaxLength (8)} ` | ` długość pliku ` | String has maximum of 8 characters. |
| ` (długość) ` | ` {filename: length (12)} ` | ` SOMEFILE. Długość txt ` | String must be exactly 12 characters long. |
| ` (min, max) ` | ` {filename: length (8, 16)} ` | ` SOMEFILE. txt ` | String must be at least 8 and has maximum of 16 characters. |
| ` min (wartość) ` | ` {Age: min (18)} ` | ` 19 ` | Integer value must be at least 18. |
| ` Max (wartość) ` | ` {Age: max (120)} ` | ` 91 ` | Integer value maximum of 120. |
| ` zakres (min, max) ` | ` {wiek: zakres (18120)} ` | ` 91 ` | Integer value must be at least 18 and maximum of 120. |
| ` Alpha ` | ` {name: Alpha} ` | ` Rick a z wyrażeniem ` | String must consist of one or more alphabetical characters ` `-` `.  Case-insensitive. |
| ` regularnym (wyrażenie) ` | ` {SSN: wyrażenie regularne (^ \\ d { {3} }- \\ d { {2} }- \\ d { {4} } $)} ` | ` 123-45-6789 ` | String must match the regular expression. See tips about defining a regular expression. |
| ` wymagana ` | ` wartość {Name: Required} ` | ` Rick "| Służy do wymuszania, że podczas generowania adresu URL jest obecna wartość, która nie jest wartością parametru. |

Wielokrotne ograniczenia rozdzielane średnikami można zastosować do jednego parametru. Na przykład następujące ograniczenie ogranicza parametr do wartości całkowitej 1 lub wyższej:

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> Ograniczenia trasy, które weryfikują adres URL i są konwertowane na typ CLR (takie jak `int` lub `DateTime` ), zawsze używają niezmiennej kultury. W tych ograniczeniach przyjęto założenie, że adres URL nie jest Lokalizowalny. Ograniczenia trasy dostarczone przez platformę nie modyfikują wartości przechowywanych w wartościach trasy. Wszystkie wartości tras analizowane na podstawie adresu URL są przechowywane jako ciągi. Na przykład, `float` ograniczenie próbuje przekonwertować wartość trasy na float, ale przekonwertowana wartość jest używana tylko do sprawdzenia, czy może być konwertowana na typ float.

## <a name="regular-expressions"></a>Wyrażenia regularne

ASP.NET Core Framework dodaje `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` do konstruktora wyrażenia regularnego. <xref:System.Text.RegularExpressions.RegexOptions>Aby uzyskać opis tych elementów członkowskich, zobacz.

Wyrażenia regularne używają ograniczników i tokenów podobnie jak w przypadku routingu i języka C#. Tokeny wyrażenia regularnego muszą być zmienione. Aby użyć wyrażenia regularnego `^\d{3}-\d{2}-\d{4}$` w routingu:

* Wyrażenie musi mieć pojedyncze ukośniki odwrotne `\` podane w ciągu jako podwójne ukośniki odwrotne `\\` w kodzie źródłowym.
* `\\`Aby można było użyć znaku ucieczki ciągu, musi nam istnieć wyrażenie regularne `\` .
* Wyrażenie regularne nie jest wymagane w `\\` przypadku używania [literałów ciągu Verbatim](/dotnet/csharp/language-reference/keywords/string).

Na znaki ogranicznika parametru routingu ucieczki,,,, `{` `}` `[` `]` podwójne znaki w wyrażeniu `{{` ,, `}` `[[` , `]]` . W poniższej tabeli przedstawiono wyrażenie regularne i wersja o zmienionym znaczeniu:

| Wyrażenie regularne    | Wyrażenie regularne o zmienionym znaczeniu     |
| ---
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

----------- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

--------------- | | `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

Wyrażenia regularne używane w routingu często zaczynają się od `^` znaku karetki i dopasowują pozycję początkową ciągu. Wyrażenia często kończą się znakiem dolara `$` i pasują do końca ciągu. `^`Znaki i `$` zapewniają, że wyrażenie regularne dopasowuje całą wartość parametru trasy. Bez `^` znaków i `$` wyrażenie regularne dopasowuje dowolny podciąg w ciągu, co jest często niepożądane. W poniższej tabeli przedstawiono przykłady i wyjaśniono, dlaczego są one zgodne lub niezgodne.

| Wyrażenie   | String (ciąg)    | Dopasowanie | Komentarz               |
| ---
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

------ | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

----- | :---: |  ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

---------- | | `[a-z]{2}`   | Witaj | Tak | Dopasowania podciągów | | `[a-z]{2}`   | 123abc456 | Tak | Dopasowania podciągów | | `[a-z]{2}`   | MZ | Tak | Wyrażenie dopasowania | | `[a-z]{2}`   | MZ | Tak | Bez uwzględniania wielkości liter | | `^[a-z]{2}$` | Witaj | Nie | Zobacz `^` i `$` powyżej | | `^[a-z]{2}$` | 123abc456 | Nie | Zobacz `^` i `$` powyżej |

Aby uzyskać więcej informacji na temat składni wyrażeń regularnych, zobacz [.NET Framework wyrażeń regularnych](/dotnet/standard/base-types/regular-expression-language-quick-reference).

Aby ograniczyć parametr do znanego zestawu możliwych wartości, użyj wyrażenia regularnego. Na przykład `{action:regex(^(list|get|create)$)}` dopasowuje `action` wartość trasy do `list` , `get` , lub `create` . Jeśli przeszedł do słownika ograniczeń, ciąg `^(list|get|create)$` jest równoważny. Ograniczenia, które są przesyłane w słowniku ograniczenia (nie wbudowane w szablon), które nie pasują do jednego ze znanych ograniczeń, są również traktowane jako wyrażenia regularne.

## <a name="custom-route-constraints"></a>Niestandardowe ograniczenia trasy

Oprócz wbudowanych ograniczeń trasy niestandardowe ograniczenia trasy mogą być tworzone przez implementację <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> interfejsu. <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>Interfejs zawiera jedną metodę, `Match` która zwraca, `true` Jeśli ograniczenie jest spełnione i `false` w przeciwnym razie.

Aby użyć niestandardowego <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> , typ ograniczenia trasy musi być zarejestrowany <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> w aplikacji w kontenerze usługi aplikacji. <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>Jest słownikiem, który mapuje klucze ograniczeń trasy do <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> implementacji, które weryfikują te ograniczenia. Aplikację <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> można zaktualizować w `Startup.ConfigureServices` ramach [usług. Wywołanie addrouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) lub przez skonfigurowanie <xref:Microsoft.AspNetCore.Routing.RouteOptions> bezpośrednio w usłudze `services.Configure<RouteOptions>` . Przykład:

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

* Wykonaj podczas generowania linku dla elementu <xref:Microsoft.AspNetCore.Routing.Route> .
* Implementacja `Microsoft.AspNetCore.Routing.IOutboundParameterTransformer` .
* Są konfigurowane przy użyciu <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> .
* Wypełnij wartość trasy parametru i Przekształć ją na nową wartość ciągu.
* Wynikiem jest użycie przekształconej wartości w wygenerowanym łączu.

Na przykład niestandardowy `slugify` transformator parametrów w wzorcu trasy `blog\{article:slugify}` z `Url.Action(new { article = "MyTestArticle" })` generowaniem `blog\my-test-article` .

Aby użyć transformatora parametrów w wzorcu trasy, skonfiguruj go najpierw za pomocą polecenia <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> w programie `Startup.ConfigureServices` :

```csharp
services.AddRouting(options =>
{
    // Replace the type and the name used to refer to it with your own
    // IOutboundParameterTransformer implementation
    options.ConstraintMap["slugify"] = typeof(SlugifyParameterTransformer);
});
```

Transformatory parametrów są używane przez platformę do przekształcania identyfikatora URI, w którym jest rozpoznawany punkt końcowy. Na przykład ASP.NET Core MVC używa transformatorów parametrów do przekształcenia wartości trasy używanej do dopasowania do `area` , `controller` , `action` , i `page` .

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

W przypadku poprzedniej trasy Akcja `SubscriptionManagementController.GetAll` jest dopasowywana do identyfikatora URI `/subscription-management/get-all` . Transformator parametrów nie zmienia wartości trasy użytych do wygenerowania linku. Na przykład dane `Url.Action("GetAll", "SubscriptionManagement")` wyjściowe `/subscription-management/get-all` .

ASP.NET Core udostępnia konwencje interfejsu API do używania transformatorów parametrów z wygenerowanymi trasami:

* ASP.NET Core MVC ma `Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention` Konwencję interfejsu API. Ta Konwencja stosuje określony transformator parametrów do wszystkich tras atrybutów w aplikacji. Transformator parametrów przekształca tokeny trasy atrybutów po ich wymianie. Aby uzyskać więcej informacji, zobacz [używanie transformatora parametrów do dostosowywania zastępowania tokenu](/aspnet/core/mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement).
* RazorStrony mają `Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention` Konwencję interfejsu API. Ta Konwencja stosuje określony transformator parametrów do wszystkich automatycznie odnalezionych Razor stron. Transformator parametrów przekształca folder i segmenty nazw plików Razor tras. Aby uzyskać więcej informacji, zobacz [używanie transformatora parametrów do dostosowywania tras stron](/aspnet/core/razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes).

## <a name="url-generation-reference"></a>Odwołanie do generacji adresów URL

Poniższy przykład pokazuje, jak wygenerować link do trasy, używając słownika wartości tras i <xref:Microsoft.AspNetCore.Routing.RouteCollection> .

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

<xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath>Wygenerowano na końcu powyższego przykładu `/package/create/123` . Słownik zawiera `operation` `id` wartości i trasy szablonu "śledzenie trasy pakietu" `package/{operation}/{id}` . Aby uzyskać szczegółowe informacje, zapoznaj się z przykładowym kodem w sekcji [Używanie oprogramowania pośredniczącego usługi routingu](#use-routing-middleware) lub [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples).

Drugi parametr <xref:Microsoft.AspNetCore.Routing.VirtualPathContext> konstruktora jest kolekcją *wartości otoczenia*. Wartości otoczenia są wygodne do użycia, ponieważ ograniczają liczbę wartości, które Deweloper musi określić w kontekście żądania. Bieżące wartości trasy bieżącego żądania są uznawane za wartości otoczenia dla generacji łącza. W akcji aplikacji ASP.NET Core MVC `About` `HomeController` , nie trzeba określać wartości trasy kontrolera do łączenia z `Index` akcją, &mdash; w której `Home` jest używana wartość otoczenia.

Wartości otoczenia, które nie pasują do parametru, są ignorowane. Wartości otoczenia są również ignorowane, gdy jawnie podana wartość przesłania wartość otoczenia. Dopasowanie występuje od lewej do prawej w adresie URL.

Wartości jawnie podane, ale które nie pasują do segmentu trasy, są dodawane do ciągu zapytania. W poniższej tabeli przedstawiono wynik przy użyciu szablonu trasy `{controller}/{action}/{id?}` .

| Wartości otoczenia                     | Wartości jawne                        | Wynik                  |
| ---
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

----------------- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

------------------- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

------------ | | Controller = "Strona główna" | Action = "informacje" | `/Home/About`|
| Controller = "Strona główna" | Controller = "Order", Action = "informacje" | `/Order/About`|
| Controller = "Home", Color = "Red" | Action = "informacje" | `/Home/About`|
| Controller = "Strona główna" | Action = "informacje", Color = "Red" | `/Home/About?color=Red`                                |

Jeśli trasa ma wartość domyślną, która nie odpowiada parametrowi, a ta wartość jest jawnie określona, musi być zgodna z wartością domyślną:

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

Generowanie linku generuje tylko łącze do tej trasy, gdy zostaną podane pasujące wartości dla `controller` i `action` .

## <a name="complex-segments"></a>Złożone segmenty

Złożone segmenty (na przykład `[Route("/x{token}y")]` ) są przetwarzane przez dopasowanie literałów z prawej strony do lewej w sposób niezachłanney. [Ten kod](https://github.com/dotnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) zawiera szczegółowy opis sposobu dopasowywania segmentów złożonych. [Przykład kodu](https://github.com/dotnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) nie jest używany przez ASP.NET Core, ale zapewnia dobre wyjaśnienie złożonych segmentów.
<!-- While that code is no longer used by ASP.NET Core for complex segment matching, it provides a good match to the current algorithm. The [current code](https://github.com/dotnet/AspNetCore/blob/91514c9af7e0f4c44029b51f05a01c6fe4c96e4c/src/Http/Routing/src/Matching/DfaMatcherBuilder.cs#L227-L244) is too abstracted from matching to be useful for understanding complex segment matching.
-->

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Routing jest odpowiedzialny za mapowanie identyfikatorów URI żądań w celu rozesłania procedur obsługi i wysyłania żądań przychodzących. Trasy są zdefiniowane w aplikacji i konfigurowane podczas uruchamiania aplikacji. Trasa może opcjonalnie wyodrębnić wartości z adresu URL zawartego w żądaniu. te wartości mogą być następnie używane do przetwarzania żądań. Przy użyciu skonfigurowanych tras z aplikacji Routing jest w stanie generować adresy URL mapowane na procedury obsługi tras.

Aby użyć najnowszych scenariuszy routingu w ASP.NET Core 2,1, określ [wersję zgodności](xref:mvc/compatibility-version) do rejestracji usług MVC w `Startup.ConfigureServices` :

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

> [!IMPORTANT]
> Ten dokument obejmuje Routing ASP.NET Core niskiego poziomu. Aby uzyskać informacje na temat ASP.NET Core routingu MVC, zobacz <xref:mvc/controllers/routing> . Aby uzyskać informacje na temat Konwencji routingu na Razor stronach, zobacz <xref:razor-pages/razor-pages-conventions> .

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="routing-basics"></a>Podstawy routingu

Większość aplikacji powinna wybrać podstawowy i opisowy schemat routingu, aby adresy URL były czytelne i zrozumiałe. Domyślna trasa konwencjonalna `{controller=Home}/{action=Index}/{id?}` :

* Obsługuje podstawowy i opisowy schemat routingu.
* Jest użytecznym punktem wyjścia dla aplikacji opartych na interfejsie użytkownika.

Deweloperzy często dodają dodatkowe trasy zwięzła do obszarów o dużym ruchu w aplikacji w wyspecjalizowanych sytuacjach (na przykład blog i punkty końcowe handlu elektronicznego) przy użyciu [routingu atrybutów](xref:mvc/controllers/routing#attribute-routing) lub dedykowanych tras konwencjonalnych.

Interfejsy API sieci Web powinny używać routingu atrybutów do modelowania funkcjonalności aplikacji jako zestawu zasobów, w których operacje są reprezentowane przez zlecenia HTTP. Oznacza to, że wiele operacji (na przykład GET, POST) dla tego samego zasobu logicznego będzie używać tego samego adresu URL. Routing atrybutu zapewnia poziom kontroli, który jest wymagany do dokładnego projektowania układu publicznego punktu końcowego interfejsu API.

RazorAplikacje stron używają domyślnego routingu konwencjonalnego do obsłużenia nazwanych zasobów w folderze *strony* aplikacji. Dostępne są dodatkowe konwencje umożliwiające dostosowywanie Razor zachowania routingu stron. Aby uzyskać więcej informacji, zobacz <xref:razor-pages/index> i <xref:razor-pages/razor-pages-conventions>.

Obsługa generowania adresów URL umożliwia tworzenie aplikacji bez adresów URL, które mają być połączone ze sobą. Ta obsługa pozwala rozpocząć od podstawowej konfiguracji routingu i zmodyfikować trasy po ustaleniu układu zasobów aplikacji.

Routing używa implementacji tras programu <xref:Microsoft.AspNetCore.Routing.IRouter> do:

* Mapuj przychodzące żądania do *obsługi tras*.
* Generuj adresy URL używane w odpowiedziach.

Domyślnie aplikacja ma jedną kolekcję tras. Po nadejściu żądania trasy w kolekcji są przetwarzane w kolejności, w jakiej istnieją w kolekcji. Platforma próbuje dopasować przychodzący adres URL żądania do trasy w kolekcji, wywołując <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> metodę dla każdej trasy w kolekcji. Odpowiedź może używać routingu do generowania adresów URL (na przykład w przypadku przekierowania lub linków) na podstawie informacji o trasach i w ten sposób unikania zakodowanych adresów URL, co ułatwia łatwość utrzymania.

System routingu ma następujące cechy:

* Składnia szablonu trasy służy do definiowania tras z parametrami trasy z tokenami.
* Dozwolona jest konfiguracja języka końcowego w stylu konwencjonalnym i stylu atrybutu.
* <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>służy do określenia, czy parametr adresu URL zawiera prawidłową wartość dla danego ograniczenia punktu końcowego.
* Modele aplikacji, takie jak MVC/ Razor Pages, rejestrują wszystkie swoje trasy, które mają przewidywalne implementację scenariuszy routingu.
* Odpowiedź może używać routingu do generowania adresów URL (na przykład w przypadku przekierowania lub linków) na podstawie informacji o trasach i w ten sposób unikania zakodowanych adresów URL, co ułatwia łatwość utrzymania.
* Generacja adresów URL jest oparta na trasach, które obsługują arbitralną rozszerzalność. <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>oferuje metody do kompilowania adresów URL.
<!-- fix [middleware](xref:fundamentals/middleware/index) -->
Routing jest połączony z potokiem [pośredniczącym](xref:fundamentals/middleware/index) przez <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> klasę. [ASP.NET Core MVC](xref:mvc/overview) dodaje Routing do potoku oprogramowania pośredniczącego w ramach swojej konfiguracji i obsługuje routing w aplikacjach MVC i Razor Pages. Aby dowiedzieć się, jak używać routingu jako składnika autonomicznego, zapoznaj się z sekcją [Korzystanie z oprogramowania do routingu](#use-routing-middleware) .

### <a name="url-matching"></a>Dopasowanie adresu URL

Dopasowywanie adresów URL to proces, za pomocą którego Routing wysyła żądanie przychodzące do *procedury obsługi*. Ten proces jest oparty na danych w ścieżce URL, ale można go rozszerzyć w celu uwzględnienia wszelkich danych w żądaniu. Możliwość wysyłania żądań do oddzielnych programów obsługi jest kluczem do skalowania rozmiaru i złożoności aplikacji.

Żądania przychodzące wprowadzają <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> , który wywołuje <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> metodę dla każdej trasy w sekwencji. <xref:Microsoft.AspNetCore.Routing.IRouter>Wystąpienie wybiera, czy *obsługiwać* żądanie przez ustawienie [RouteContext. Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler*) na wartość różną od null <xref:Microsoft.AspNetCore.Http.RequestDelegate> . Jeśli trasa ustawi procedurę obsługi dla żądania, przetwarzanie trasy zostanie zatrzymane, a procedura obsługi zostanie wywołana w celu przetworzenia żądania. Jeśli nie znaleziono procedury obsługi trasy do przetworzenia żądania, oprogramowanie pośredniczące przekazuje żądanie do następnego oprogramowania pośredniczącego w potoku żądania.

Podstawowe dane wejściowe <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> to [RouteContext. HttpContext](xref:Microsoft.AspNetCore.Routing.RouteContext.HttpContext*) skojarzone z bieżącym żądaniem. [RouteContext. Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler) i [RouteContext. RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData*) są ustawiane jako wyjście po dopasowaniu trasy.

Dopasowanie, które wywołuje <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> również ustawia właściwości [RouteContext. RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) na odpowiednie wartości na podstawie przetwarzania żądania wykonanego do tej pory.

[RouteData. Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) to słownik *wartości tras* uzyskanych z trasy. Te wartości są zwykle określane przez tokenizowanie jako adres URL i mogą służyć do akceptowania danych wejściowych użytkownika lub do dalszej akceptacji decyzji w aplikacji.

[RouteData. DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) to zbiór właściwości dodatkowych danych dotyczących dopasowanej trasy. <xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*>zapewnia obsługę kojarzenia danych stanu z każdą trasą, dzięki czemu aplikacja może podejmować decyzje na podstawie dopasowanej trasy. Te wartości są zdefiniowane przez dewelopera i **nie** mają wpływu na zachowanie routingu. Ponadto wartości umieszczane w [RouteData. Datatokeny](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) mogą być dowolnego typu, w przeciwieństwie do [RouteData. wartości](xref:Microsoft.AspNetCore.Routing.RouteData.Values), które muszą być konwertowane do i z ciągów.

[RouteData. routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) to lista tras, które brały udział w pomyślnie dopasowane do żądania. Trasy mogą być zagnieżdżone wewnątrz siebie. <xref:Microsoft.AspNetCore.Routing.RouteData.Routers>Właściwość odzwierciedla ścieżkę przez logiczne drzewo tras, które spowodowały dopasowanie. Ogólnie rzecz biorąc, pierwszy element w <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> jest kolekcją tras i powinien być używany do generowania adresów URL. Ostatnim elementem w programie <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> jest program obsługi trasy, który został dopasowany.

<a name="lg"></a>

### <a name="url-generation"></a>Generowanie adresu URL

Generowanie adresu URL to proces, za pomocą którego Routing może utworzyć ścieżkę URL na podstawie zestawu wartości trasy. Pozwala to na logiczne rozdzielenie między programami obsługi tras i adresami URL, które uzyskują do nich dostęp.

Generowanie adresu URL następuje podobny proces iteracyjny, ale rozpoczyna się od kodu użytkownika lub struktury wywołującego <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> metodę kolekcji tras. Każda *trasa* ma <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> metodę wywoływana w sekwencji do momentu zwrócenia wartości innej niż null <xref:Microsoft.AspNetCore.Routing.VirtualPathData> .

Podstawowe dane wejściowe <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> :

* [VirtualPathContext. HttpContext](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext)
* [VirtualPathContext. wartości](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values)
* [VirtualPathContext.AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues)

Trasy wykorzystują głównie wartości trasy dostarczone przez <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> i, <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> Aby zdecydować, czy możliwe jest WYGENEROWANIE adresu URL i wartości do uwzględnienia. <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues>Są to zbiór wartości tras, które zostały utworzone na podstawie dopasowania bieżącego żądania. Z kolei <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> są wartościami trasy, które określają sposób generowania żądanego adresu URL dla bieżącej operacji. <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext>W przypadku trasy należy uzyskać usługi lub dodatkowe dane skojarzone z bieżącym kontekstem.

> [!TIP]
> Pomyśl o [VirtualPathContext. wartości](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values*) jako zestawu zastąpień dla [VirtualPathContext. AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues*). Generowanie adresów URL próbuje ponownie użyć wartości trasy z bieżącego żądania w celu wygenerowania adresów URL dla linków przy użyciu tych samych wartości trasy lub trasy.

Wynik <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> jest <xref:Microsoft.AspNetCore.Routing.VirtualPathData> . <xref:Microsoft.AspNetCore.Routing.VirtualPathData>jest równoległe z <xref:Microsoft.AspNetCore.Routing.RouteData> . <xref:Microsoft.AspNetCore.Routing.VirtualPathData>zawiera wartość <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> dla wyjściowego adresu URL oraz kilka dodatkowych właściwości, które powinny być ustawiane przez trasę.

Właściwość [VirtualPathData. VirtualPath](xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath*) zawiera *ścieżkę wirtualną* wygenerowaną przez trasę. W zależności od potrzeb może być konieczne dalsze przetworzenie ścieżki. Jeśli chcesz renderować wygenerowany adres URL w formacie HTML, poprzedź ścieżkę podstawową aplikacji.

[VirtualPathData. router](xref:Microsoft.AspNetCore.Routing.VirtualPathData.Router*) jest odwołaniem do trasy, która pomyślnie wygenerowała adres URL.

Właściwości [VirtualPathData. DataTokens](xref:Microsoft.AspNetCore.Routing.VirtualPathData.DataTokens*) to słownik dodatkowych danych związanych z trasą, która wygenerowała adres URL. Jest to równoległy [RouteData. DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*).

### <a name="create-routes"></a>Tworzenie tras

Routing udostępnia <xref:Microsoft.AspNetCore.Routing.Route> klasę jako standardową implementację programu <xref:Microsoft.AspNetCore.Routing.IRouter> . <xref:Microsoft.AspNetCore.Routing.Route>używa składni *szablonu trasy* do definiowania wzorców do dopasowania względem ścieżki URL, gdy <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> jest wywoływana. <xref:Microsoft.AspNetCore.Routing.Route>używa tego samego szablonu trasy do wygenerowania adresu URL, gdy <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> jest wywoływana.

Większość aplikacji tworzy trasy przez wywołanie <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> lub jedną z podobnych metod rozszerzających zdefiniowanych w systemie <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> . Dowolna z <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> metod rozszerzających tworzy wystąpienie <xref:Microsoft.AspNetCore.Routing.Route> i dodaje je do kolekcji tras.

<xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>nie akceptuje parametru procedury obsługi trasy. <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>dodaje tylko trasy, które są obsługiwane przez <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*> . Domyślna procedura obsługi to `IRouter` , a program obsługi może nie obsłużyć żądania. Na przykład ASP.NET Core MVC jest zwykle skonfigurowany jako domyślny program obsługi, który obsługuje tylko żądania zgodne z dostępnym kontrolerem i akcją. Aby dowiedzieć się więcej na temat routingu w MVC, zobacz <xref:mvc/controllers/routing> .

Poniższy przykład kodu jest przykładem <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> wywołania używanego przez typową definicję trasy MVC ASP.NET Core:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Ten szablon dopasowuje ścieżkę URL i wyodrębnia wartości tras. Na przykład ścieżka `/Products/Details/17` generuje następujące wartości trasy: `{ controller = Products, action = Details, id = 17 }` .

Wartości tras są określane przez podzielenie ścieżki URL na segmenty i dopasowanie do każdego segmentu przy użyciu nazwy *parametru trasy* w szablonie trasy. Parametry trasy są nazywane. Parametry zdefiniowane przez ujęcie nazwy parametru w nawiasach klamrowych `{ ... }` .

Poprzedni szablon może również pasować do ścieżki URL `/` i generować wartości `{ controller = Home, action = Index }` . Dzieje się tak, `{controller}` ponieważ `{action}` Parametry i trasy mają wartości domyślne, a `id` parametr trasy jest opcjonalny. Znak równości ( `=` ), po którym następuje wartość po nazwie parametru trasy definiuje wartość domyślną dla parametru. Znak zapytania ( `?` ) po nazwie parametru trasy definiuje opcjonalny parametr.

Parametry trasy z wartością domyślną *zawsze* generują wartość trasy w przypadku dopasowania trasy. Parametry opcjonalne nie generują wartości trasy, jeśli nie ma odpowiedniego segmentu ścieżki adresu URL. Szczegółowe opisy scenariuszy i składni szablonów tras można znaleźć w sekcji [Dokumentacja dotycząca szablonu trasy](#route-template-reference) .

W poniższym przykładzie definicja parametru trasy `{id:int}` definiuje [ograniczenie trasy](#route-constraint-reference) dla `id` parametru trasy:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

Ten szablon dopasowuje ścieżkę URL, taką jak, `/Products/Details/17` ale nie `/Products/Details/Apples` . Ograniczenia trasy implementują <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> i sprawdzają wartości routingu w celu ich zweryfikowania. W tym przykładzie wartość trasy `id` musi być możliwa do przekonwertowania na liczbę całkowitą. Aby uzyskać wyjaśnienie ograniczeń trasy dostarczonych przez platformę, zobacz temat [ograniczenia trasy](#route-constraint-reference) .

Dodatkowe przeciążenia <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> wartości akceptują dla `constraints` , `dataTokens` i `defaults` . Typowym użyciem tych parametrów jest przekazanie anonimowo wpisanego obiektu, gdzie nazwy właściwości typu anonimowego są zgodne z nazwami parametrów trasy.

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
> Wbudowana Składnia służąca do definiowania ograniczeń i wartości domyślnych może być wygodna dla prostych tras. Istnieją jednak scenariusze, takie jak tokeny danych, które nie są obsługiwane przez składnię wbudowaną.

Poniższy przykład ilustruje kilka dodatkowych scenariuszy:

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{*article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

Poprzedni szablon pasuje do ścieżki URL, tak jak `/Blog/All-About-Routing/Introduction` i wyodrębnia wartości `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }` . Domyślne wartości trasy dla `controller` i `action` są generowane przez trasę, nawet jeśli w szablonie nie ma odpowiednich parametrów trasy. Wartości domyślne można określić w szablonie trasy. `article`Parametr Route jest definiowany jako *przechwycenie* przez wygląd gwiazdki ( `*` ) przed nazwą parametru trasy. Catch-wszystkie parametry tras przechwytują resztę ścieżki URL i mogą również pasować do pustego ciągu.

Poniższy przykład dodaje ograniczenia trasy i tokeny danych:

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

Poprzedni szablon pasuje do ścieżki URL, na przykład `/en-US/Products/5` i wyodrębnia wartości `{ controller = Products, action = Details, id = 5 }` i tokeny danych `{ locale = en-US }` .

![Lokalne tokeny systemu Windows](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a>Generowanie adresu URL klasy trasy

<xref:Microsoft.AspNetCore.Routing.Route>Klasa może również wykonywać generowanie adresów URL przez połączenie zestawu wartości tras z jego szablonem trasy. Jest to logicznie proces odwrotny pasujący do ścieżki URL.

> [!TIP]
> Aby lepiej zrozumieć generowanie adresów URL, Załóżmy, jaki adres URL ma zostać wygenerowany, a następnie pomyśl o sposobie dopasowania szablonu trasy do tego adresu URL. Jakie wartości zostałyby wygenerowane? Jest to sztywny odpowiednik działania generowania adresów URL w <xref:Microsoft.AspNetCore.Routing.Route> klasie.

Poniższy przykład używa ogólnej trasy domyślnej ASP.NET Core MVC:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

W przypadku wartości trasy `{ controller = Products, action = List }` `/Products/List` zostanie wygenerowany adres URL. Wartości trasy są zastępowane odpowiednimi parametrami trasy w celu utworzenia ścieżki URL. Ponieważ `id` jest to opcjonalny parametr trasy, adres URL został pomyślnie wygenerowany bez wartości dla `id` .

W przypadku wartości trasy `{ controller = Home, action = Index }` `/` zostanie wygenerowany adres URL. Podane wartości trasy są zgodne z wartościami domyślnymi, a segmenty odpowiadające wartościom domyślnym są bezpiecznie pomijane.

Oba adresy URL wygenerowały rundę z następującą definicją trasy ( `/Home/Index` i `/` ) tworzą te same wartości trasy, które zostały użyte do WYGENEROWANIA adresu URL.

> [!NOTE]
> Aplikacja używająca ASP.NET Core MVC powinna używać <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> do generowania adresów URL zamiast bezpośredniego wywoływania routingu.

Aby uzyskać więcej informacji na temat generowania adresów URL, zobacz sekcję [informacje dotyczące generowania adresów URL](#url-generation-reference) .

## <a name="use-routing-middleware"></a>Korzystanie z oprogramowania pośredniczącego routingu

Odwołuje się do [pakietu Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) w pliku projektu aplikacji.

Dodaj Routing do kontenera usługi w `Startup.ConfigureServices` :

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

Trasy muszą być skonfigurowane w `Startup.Configure` metodzie. Przykładowa aplikacja używa następujących interfejsów API:

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>: Dopasowuje tylko żądania HTTP GET.
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

W poniższej tabeli przedstawiono odpowiedzi z podanym identyfikatorem URI.

| Identyfikator URI                    | Odpowiedź                                          |
| ---
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

----------- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

------------------------- | | `/package/create/3`    | Cześć! Wartości trasy: [Operation, Create], [ID, 3] | | `/package/track/-3`    | Cześć! Wartości trasy: [Operation, Track], [ID,-3] | | `/package/track/-3/`   | Cześć! Wartości trasy: [Operation, Track], [ID,-3] | | `/package/track/`      | Żądanie przepada w, brak dopasowania.              | | `GET /hello/Joe`       | Witaj, Jan!                                          | | `POST /hello/Joe`      | Żądanie przepada w, dopasowuje tylko HTTP GET. | | `GET /hello/Joe/Smith` | Żądanie przepada w, brak dopasowania.              |

W przypadku konfigurowania pojedynczej trasy Wywołaj <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*> przekazywanie w `IRouter` wystąpieniu. Nie musisz używać <xref:Microsoft.AspNetCore.Routing.RouteBuilder> .

Struktura zawiera zestaw metod rozszerzających do tworzenia tras ( <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions> ):

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

Niektóre z wymienionych metod, takie jak <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> , wymagają <xref:Microsoft.AspNetCore.Http.RequestDelegate> . <xref:Microsoft.AspNetCore.Http.RequestDelegate>Jest używany jako *program obsługi trasy* , gdy trasa pasuje. Inne metody w tej rodzinie umożliwiają skonfigurowanie potoku oprogramowania pośredniczącego, które ma być używane jako procedura obsługi trasy. Jeśli `Map*` Metoda nie akceptuje procedury obsługi, takiej jak <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*> , używa <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*> .

`Map[Verb]`Metody służą do ograniczania trasy do zlecenia HTTP w nazwie metody. Na przykład zobacz <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> i <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*> .

## <a name="route-template-reference"></a>Odwołanie do szablonu trasy

Tokeny w nawiasach klamrowych ( `{ ... }` ) definiują *Parametry trasy* , które są powiązane w przypadku dopasowania trasy. Można zdefiniować więcej niż jeden parametr trasy w segmencie trasy, ale muszą one być oddzielone wartością literału. Na przykład `{controller=Home}{action=Index}` nie jest prawidłową trasą, ponieważ nie ma wartości literału między `{controller}` i `{action}` . Te parametry trasy muszą mieć nazwę i mogą mieć określone dodatkowe atrybuty.

Tekst literału inny niż parametry trasy (na przykład `{id}` ) i separator ścieżki `/` muszą być zgodne z tekstem w adresie URL. W dopasowaniu do tekstu nie jest rozróżniana wielkość liter i w oparciu o zdekodowaną reprezentację ścieżki URL. Aby dopasować Ogranicznik parametru trasy literału ( `{` lub `}` ), należy wprowadzić ogranicznik przez powtórzenie znaku ( `{{` lub `}}` ).

Wzorce adresów URL, które próbują przechwycić nazwę pliku z opcjonalnym rozszerzeniem pliku, mają dodatkowe uwagi. Rozważmy na przykład szablon `files/{filename}.{ext?}` . Gdy wartości obu `filename` i `ext` istnieją, są wypełniane obie wartości. Jeśli `filename` w adresie URL znajduje się tylko wartość parametru, trasa pasuje do, ponieważ końcowy okres ( `.` ) jest opcjonalny. Następujące adresy URL pasują do tej trasy:

* `/files/myFile.txt`
* `/files/myFile`

Możesz użyć gwiazdki ( `*` ) jako prefiksu do parametru trasy, aby powiązać z resztą identyfikatora URI. Jest to nazywane parametrem *"catch-all"* . Na przykład `blog/{*slug}` dopasowuje dowolny identyfikator URI, który rozpoczyna się od `/blog` i ma dowolną wartość, która jest przypisana do `slug` wartości trasy. Catch-wszystkie parametry można także dopasować do pustego ciągu.

Parametr catch-all wyprowadza odpowiednie znaki, gdy trasa jest używana do generowania adresu URL, włącznie z separatorami ścieżki ( `/` ). Na przykład trasa `foo/{*path}` z wartościami trasy jest `{ path = "my/path" }` generowana `foo/my%2Fpath` . Zwróć uwagę na odwrócony ukośnik.

Parametry trasy mogą mieć *wartości domyślne* , określając wartość domyślną po nazwie parametru oddzielone znakiem równości ( `=` ). Na przykład `{controller=Home}` definiuje `Home` jako wartość domyślną dla `controller` . Wartość domyślna jest używana, jeśli żadna wartość nie jest obecna w adresie URL dla parametru. Parametry trasy są opcjonalne przez dołączenie znaku zapytania ( `?` ) na końcu nazwy parametru, jak w `id?` . Różnica między wartościami opcjonalnymi i domyślnymi parametrami trasy polega na tym, że parametr trasy z wartością domyślną zawsze tworzy wartość, &mdash; a opcjonalny parametr ma wartość tylko wtedy, gdy wartość jest dostarczana przez adres URL żądania.

Parametry trasy mogą mieć ograniczenia, które muszą być zgodne z wartością trasy powiązaną z adresem URL. Dodanie dwukropka ( `:` ) i nazwy ograniczenia po nazwie parametru trasy określa *ograniczenie wbudowane* dla parametru trasy. Jeśli ograniczenie wymaga argumentów, są one ujęte w nawiasy ( `(...)` ) po nazwie ograniczenia. Można określić wiele ograniczeń wbudowanych, dołączając inną dwukropek ( `:` ) i nazwę ograniczenia.

Nazwa i argumenty ograniczenia są przekazane do usługi w <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> celu utworzenia wystąpienia <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> do użycia w przetwarzaniu adresów URL. Na przykład szablon trasy `blog/{article:minlength(10)}` określa `minlength` ograniczenie z argumentem `10` . Aby uzyskać więcej informacji na temat ograniczeń trasy i listę ograniczeń zapewnianych przez platformę, zobacz sekcję [odwołanie do ograniczenia trasy](#route-constraint-reference) .

W poniższej tabeli przedstawiono przykładowe szablony tras i ich zachowanie.

| Szablon trasy                           | Przykładowy pasujący identyfikator URI    | Identyfikator URI żądania&hellip;                                                    |
| ---
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-------------------- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

------------ | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

------------------------------------- | | `hello`                                  | `/hello`                | Dopasowuje tylko jedną ścieżkę `/hello` .                                     | | `{Page=Home}`                            | `/`                     | Dopasowuje i ustawia `Page` jako `Home` .                                         | | `{Page=Home}`                            | `/Contact`              | Dopasowuje i ustawia `Page` jako `Contact` .                                      | | `{controller}/{action}/{id?}`            | `/Products/List`        | Mapuje do `Products` kontrolera i `List` akcji.                       | | `{controller}/{action}/{id?}`            | `/Products/Details/123` | Mapuje do `Products` kontrolera i `Details` akcji ( `id` ustawienie na 123). | | `{controller=Home}/{action=Index}/{id?}` | `/`                     | Mapuje na `Home` kontroler i `Index` metodę ( `id` jest ignorowana).        |

Użycie szablonu jest ogólnie najprostszym podejściem do routingu. Ograniczenia i wartości domyślne można także określić poza szablonem trasy.

> [!TIP]
> Włącz [Rejestrowanie](xref:fundamentals/logging/index) , aby zobaczyć, jak wbudowane implementacje routingu, takie jak <xref:Microsoft.AspNetCore.Routing.Route> , dopasowują żądania.

## <a name="route-constraint-reference"></a>Odwołanie do ograniczenia trasy

Ograniczenia trasy są wykonywane, gdy nastąpiło dopasowanie do przychodzącego adresu URL, a Ścieżka adresu URL jest zgodna z wartościami trasy. Ograniczenia trasy zwykle sprawdzają wartość trasy skojarzoną za pośrednictwem szablonu trasy i podejmują decyzję o tym, czy wartość jest akceptowalna. Niektóre ograniczenia trasy używają danych poza wartością trasy, aby uwzględnić, czy żądanie może być kierowane. Na przykład <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> może akceptować lub odrzucać żądanie na podstawie jego zlecenia http. Ograniczenia są używane w żądaniach routingu i generowania linków.

> [!WARNING]
> Nie używaj ograniczeń **sprawdzania poprawności danych wejściowych**. Jeśli ograniczenia są używane do **sprawdzania poprawności danych wejściowych**, nieprawidłowe dane wejściowe w odpowiedzi *404 — nie znaleziono* , zamiast *żądania 400-złe* z odpowiednim komunikatem o błędzie. Ograniczenia trasy są **używane do** odróżniania podobnych tras, a nie do sprawdzania danych wejściowych dla określonej trasy.

W poniższej tabeli przedstawiono przykładowe ograniczenia trasy i ich oczekiwane zachowanie.

| ograniczenie | Przykład | Przykładowe dopasowania | Uwagi |
| ---
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

----- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

---- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-------- | ----- | | `int` | `{id:int}` | `123456789`, `-123456789` | Dopasowuje dowolną liczbę całkowitą | | `bool` | `{active:bool}` | `true`, `FALSE` | Dopasowuje lub (bez uwzględniania `true` `false` wielkości liter) | | `datetime`  |  `{dob:datetime}`  |  `2016-12-31` , `2016-12-31 7:32pm` | Dopasowuje prawidłową `DateTime` wartość w niezmiennej kulturze. Zobacz poprzednie ostrzeżenie. | | `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Dopasowuje prawidłową `decimal` wartość w niezmiennej kulturze. Zobacz poprzednie ostrzeżenie. | | `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | Dopasowuje prawidłową `double` wartość w niezmiennej kulturze. Zobacz poprzednie ostrzeżenie. | | `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | Dopasowuje prawidłową `float` wartość w niezmiennej kulturze. Zobacz poprzednie ostrzeżenie. | | `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Dopasowuje prawidłową `Guid` wartość | | `long`  |  `{ticks:long}`  |  `123456789` , `-123456789` | Pasuje do prawidłowej `long` wartości | | `minlength(value)`  |  `{username:minlength(4)}`  |  `Rick` | Ciąg musi składać się z co najmniej 4 znaków | | `maxlength(value)` | `{filename:maxlength(8)}` | `Richard` | Ciąg nie może zawierać więcej niż 8 znaków | | `length(length)` | `{filename:length(12)}` | `somefile.txt` | Ciąg musi zawierać dokładnie 12 znaków | | `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | Ciąg musi zawierać od 8 do 16 znaków | | `min(value)` | `{age:min(18)}` | `19` | Wartość całkowita musi być co najmniej 18 | | `max(value)` | `{age:max(120)}` | `91` | Wartość całkowita nie może być większa niż 120 | | `range(min,max)` | `{age:range(18,120)}` | `91` | Wartość całkowita musi zawierać co najmniej 18, ale nie więcej niż 120 | | `alpha` | `{name:alpha}` | `Rick` | Ciąg musi zawierać co najmniej jeden znak alfabetyczny ( `a` - `z` , bez uwzględniania wielkości liter) | | `regex(expression)`  |  `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}`  |  `123-45-6789` | Ciąg musi być zgodny z wyrażeniem regularnym (zobacz Porady dotyczące definiowania wyrażenia regularnego) | | `required` | `{name:required}` | `Rick` | Służy do wymuszania, że podczas generowania adresu URL jest obecna wartość, która nie jest parametrem |

Wielokrotne ograniczenia rozdzielane średnikami można zastosować do jednego parametru. Na przykład następujące ograniczenie ogranicza parametr do wartości całkowitej 1 lub wyższej:

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> Ograniczenia trasy, które weryfikują adres URL i są konwertowane na typ CLR (takie jak `int` lub `DateTime` ), zawsze używają niezmiennej kultury. W tych ograniczeniach przyjęto założenie, że adres URL nie jest Lokalizowalny. Ograniczenia trasy dostarczone przez platformę nie modyfikują wartości przechowywanych w wartościach trasy. Wszystkie wartości tras analizowane na podstawie adresu URL są przechowywane jako ciągi. Na przykład, `float` ograniczenie próbuje przekonwertować wartość trasy na float, ale przekonwertowana wartość jest używana tylko do sprawdzenia, czy może być konwertowana na typ float.

## <a name="regular-expressions"></a>Wyrażenia regularne

ASP.NET Core Framework dodaje `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` do konstruktora wyrażenia regularnego. <xref:System.Text.RegularExpressions.RegexOptions>Aby uzyskać opis tych elementów członkowskich, zobacz.

Wyrażenia regularne używają ograniczników i tokenów podobnie jak w przypadku routingu i języka C#. Tokeny wyrażenia regularnego muszą być zmienione. Aby użyć wyrażenia regularnego `^\d{3}-\d{2}-\d{4}$` w routingu, wyrażenie musi mieć `\` (pojedynczy ukośnik odwrotny) podany w ciągu jako `\\` (podwójny ukośnik odwrotny) w pliku źródłowym C# w celu ucieczki `\` znaku ucieczki ciągu (chyba że używane są [literały ciągu Verbatim](/dotnet/csharp/language-reference/keywords/string)). Na znaki ogranicznika parametru routingu ucieczki ( `{` , `}` , `[` , `]` ), podwójne znaki w wyrażeniu ( `{{` , `}` ,, `[[` `]]` ). W poniższej tabeli przedstawiono wyrażenie regularne i wersja z ucieczką.

| Wyrażenie regularne    | Wyrażenie regularne o zmienionym znaczeniu     |
| ---
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

----------- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

--------------- | | `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

Wyrażenia regularne używane w routingu często zaczynają się od znaku daszka ( `^` ) i dopasowują pozycję początkową ciągu. Wyrażenia często kończą się znakiem dolara ( `$` ) i końcem ciągu. `^`Znaki i `$` zapewniają, że wyrażenie regularne dopasowuje całą wartość parametru trasy. Bez `^` znaków i `$` wyrażenie regularne dopasowuje dowolny podciąg w ciągu, co jest często niepożądane. W poniższej tabeli przedstawiono przykłady i wyjaśniono, dlaczego są one zgodne lub niezgodne.

| Wyrażenie   | String (ciąg)    | Dopasowanie | Komentarz               |
| ---
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

------ | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

----- | :---: |  ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

---------- | | `[a-z]{2}`   | Witaj | Tak | Dopasowania podciągów | | `[a-z]{2}`   | 123abc456 | Tak | Dopasowania podciągów | | `[a-z]{2}`   | MZ | Tak | Wyrażenie dopasowania | | `[a-z]{2}`   | MZ | Tak | Bez uwzględniania wielkości liter | | `^[a-z]{2}$` | Witaj | Nie | Zobacz `^` i `$` powyżej | | `^[a-z]{2}$` | 123abc456 | Nie | Zobacz `^` i `$` powyżej |

Aby uzyskać więcej informacji na temat składni wyrażeń regularnych, zobacz [.NET Framework wyrażeń regularnych](/dotnet/standard/base-types/regular-expression-language-quick-reference).

Aby ograniczyć parametr do znanego zestawu możliwych wartości, użyj wyrażenia regularnego. Na przykład `{action:regex(^(list|get|create)$)}` dopasowuje `action` wartość trasy do `list` , `get` , lub `create` . Jeśli przeszedł do słownika ograniczeń, ciąg `^(list|get|create)$` jest równoważny. Ograniczenia, które są przesyłane w słowniku ograniczenia (nie wbudowane w szablon), które nie pasują do jednego ze znanych ograniczeń, są również traktowane jako wyrażenia regularne.

## <a name="custom-route-constraints"></a>Niestandardowe ograniczenia trasy

Oprócz wbudowanych ograniczeń trasy niestandardowe ograniczenia trasy mogą być tworzone przez implementację <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> interfejsu. <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>Interfejs zawiera jedną metodę, `Match` która zwraca, `true` Jeśli ograniczenie jest spełnione i `false` w przeciwnym razie.

Aby użyć niestandardowego <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> , typ ograniczenia trasy musi być zarejestrowany <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> w aplikacji w kontenerze usługi aplikacji. <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>Jest słownikiem, który mapuje klucze ograniczeń trasy do <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> implementacji, które weryfikują te ograniczenia. Aplikację <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> można zaktualizować w `Startup.ConfigureServices` ramach [usług. Wywołanie addrouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) lub przez skonfigurowanie <xref:Microsoft.AspNetCore.Routing.RouteOptions> bezpośrednio w usłudze `services.Configure<RouteOptions>` . Przykład:

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

## <a name="url-generation-reference"></a>Odwołanie do generacji adresów URL

Poniższy przykład pokazuje, jak wygenerować link do trasy, używając słownika wartości tras i <xref:Microsoft.AspNetCore.Routing.RouteCollection> .

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

<xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath>Wygenerowano na końcu powyższego przykładu `/package/create/123` . Słownik zawiera `operation` `id` wartości i trasy szablonu "śledzenie trasy pakietu" `package/{operation}/{id}` . Aby uzyskać szczegółowe informacje, zapoznaj się z przykładowym kodem w sekcji [Używanie oprogramowania pośredniczącego usługi routingu](#use-routing-middleware) lub [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples).

Drugi parametr <xref:Microsoft.AspNetCore.Routing.VirtualPathContext> konstruktora jest kolekcją *wartości otoczenia*. Wartości otoczenia są wygodne do użycia, ponieważ ograniczają liczbę wartości, które Deweloper musi określić w kontekście żądania. Bieżące wartości trasy bieżącego żądania są uznawane za wartości otoczenia dla generacji łącza. W akcji aplikacji ASP.NET Core MVC `About` `HomeController` , nie trzeba określać wartości trasy kontrolera do łączenia z `Index` akcją, &mdash; w której `Home` jest używana wartość otoczenia.

Wartości otoczenia, które nie pasują do parametru, są ignorowane. Wartości otoczenia są również ignorowane, gdy jawnie podana wartość przesłania wartość otoczenia. Dopasowanie występuje od lewej do prawej w adresie URL.

Wartości jawnie podane, ale które nie pasują do segmentu trasy, są dodawane do ciągu zapytania. W poniższej tabeli przedstawiono wynik przy użyciu szablonu trasy `{controller}/{action}/{id?}` .

| Wartości otoczenia                     | Wartości jawne                        | Wynik                  |
| ---
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

----------------- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

------------------- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

------------ | | Controller = "Strona główna" | Action = "informacje" | `/Home/About`|
| Controller = "Strona główna" | Controller = "Order", Action = "informacje" | `/Order/About`|
| Controller = "Home", Color = "Red" | Action = "informacje" | `/Home/About`|
| Controller = "Strona główna" | Action = "informacje", Color = "Red" | `/Home/About?color=Red`                                |

Jeśli trasa ma wartość domyślną, która nie odpowiada parametrowi, a ta wartość jest jawnie określona, musi być zgodna z wartością domyślną:

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

Generowanie linku generuje tylko łącze do tej trasy, gdy zostaną podane pasujące wartości dla `controller` i `action` .

## <a name="complex-segments"></a>Złożone segmenty

Złożone segmenty (na przykład `[Route("/x{token}y")]` ) są przetwarzane przez dopasowanie literałów z prawej strony do lewej w sposób niezachłanney. [Ten kod](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) zawiera szczegółowy opis sposobu dopasowywania segmentów złożonych. [Przykład kodu](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) nie jest używany przez ASP.NET Core, ale zapewnia dobre wyjaśnienie złożonych segmentów.

::: moniker-end
