---
title: Filtry w ASP.NET Core
author: Rick-Anderson
description: Dowiedz się, jak działają filtry i jak korzystać z nich w ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
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
uid: mvc/controllers/filters
ms.openlocfilehash: d075faa951a34fb3856b54eb9e21593b6616b4f1
ms.sourcegitcommit: bce62ceaac7782e22d185814f2e8532c84efa472
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94673968"
---
# <a name="filters-in-aspnet-core"></a>Filtry w ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

[Kirka Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tomasz Dykstra](https://github.com/tdykstra/)i [Steve Smith](https://ardalis.com/)

*Filtry* w ASP.NET Core umożliwiają uruchamianie kodu przed określonymi etapami w potoku przetwarzania żądań lub po nich.

Filtry wbudowane obsługują zadania takie jak:

* Autoryzacja (zapobieganie dostępowi do zasobów, dla których użytkownik nie jest autoryzowany).
* Buforowanie odpowiedzi (krótkie obwody potoku żądania w celu zwrócenia buforowanej odpowiedzi).

Filtry niestandardowe mogą być tworzone w celu obsłużenia krzyżowego rozcinania. Przykłady zagadnień związanych z rozcinaniem obejmują obsługę błędów, buforowanie, konfigurację, autoryzację i rejestrowanie.  Filtry unikają duplikowania kodu. Na przykład filtr wyjątków obsługujący błędy może skonsolidować obsługę błędów.

Ten dokument ma zastosowanie do Razor stron, kontrolerów interfejsu API i kontrolerów z widokami. Filtry nie działają bezpośrednio ze [ Razor składnikami](xref:blazor/components/index). Filtr może mieć tylko pośredni wpływ na składnik, gdy:

* Składnik jest osadzony na stronie lub widoku.
* Strona lub kontroler/widok używają filtru.

[Wyświetl lub Pobierz przykład](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([jak pobrać](xref:index#how-to-download-a-sample)).

## <a name="how-filters-work"></a>Jak działają filtry

Filtry są uruchamiane w *potoku wywołania akcji ASP.NET Core*, czasami określane jako *potok filtru*. Potok filtru jest uruchamiany po ASP.NET Core wybiera akcję do wykonania.

![Żądanie jest przetwarzane przez inne oprogramowanie pośredniczące, kierowanie oprogramowania pośredniczącego, wybór akcji i potok akcji wywołania. Przetwarzanie żądań jest kontynuowane przez wybór akcji, kierowanie oprogramowania pośredniczącego i różnych innych programów pośredniczących przed wysłaniem odpowiedzi do klienta.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a>Typy filtrów

Każdy typ filtru jest wykonywany na innym etapie w potoku filtru:

* [Filtry autoryzacji](#authorization-filters) są uruchamiane jako pierwsze i są używane do określenia, czy użytkownik jest autoryzowany do żądania. Filtry autoryzacji skracają obwód w przypadku, gdy żądanie nie jest autoryzowane.

* [Filtry zasobów](#resource-filters):

  * Uruchom po autoryzacji.  
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> uruchamia kod przed resztą potoku filtru. Na przykład `OnResourceExecuting` uruchamia kod przed powiązaniem modelu.
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> uruchamia kod po zakończeniu reszty potoku.

* [Filtry akcji](#action-filters):

  * Uruchom kod bezpośrednio przed i po wywołaniu metody akcji.
  * Może zmienić argumenty przekazane do akcji.
  * Można zmienić wynik zwrócony z akcji.
  * **Nie** są obsługiwane na Razor stronach.

* [Filtry wyjątków](#exception-filters) stosują zasady globalne do nieobsłużonych wyjątków, które występują przed zapisaniem treści odpowiedzi.

* [Filtry wyników](#result-filters) uruchamiają kod bezpośrednio przed i po wykonaniu wyników akcji. Są one uruchamiane tylko wtedy, gdy metoda akcji została wykonana pomyślnie. Są one przydatne dla logiki, która musi być w trakcie wyświetlania lub wykonywania w programie formatującego.

Na poniższym diagramie przedstawiono sposób, w jaki typy filtrów współdziałają w potoku filtru.

![Żądanie jest przetwarzane przez filtry autoryzacji, filtry zasobów, powiązania modelu, filtry akcji, wykonywanie akcji i konwersję wyników akcji, filtry wyjątków, filtry wynikowe i wykonywanie wyniku. W ten sposób żądanie jest przetwarzane tylko przez filtry wyników i filtry zasobów przed wysłaniem odpowiedzi do klienta.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a>Implementacja

Filtry obsługują implementacje synchroniczne i asynchroniczne za pomocą różnych definicji interfejsu.

Filtry synchroniczne uruchamiają kod przed i po fazie potoku. Na przykład, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> jest wywoływana przed wywołaniem metody akcji. <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> jest wywoływana po powrocie metody akcji.

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

W poprzednim kodzie funkcja myFunction [jest funkcją](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs) narzędziową w [przykładowym pobieraniu](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs).

Filtry asynchroniczne definiują `On-Stage-ExecutionAsync` metodę. Na przykład <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*> :

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

W poprzednim kodzie, `SampleAsyncActionFilter` ma <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> ( `next` ), który wykonuje metodę akcji.

### <a name="multiple-filter-stages"></a>Wiele etapów filtrowania

Interfejsy dla wielu etapów filtru można zaimplementować w pojedynczej klasie. Na przykład <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> Klasa implementuje:

* Synchroniczne: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> i  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter>
* Asynchroniczny: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> i <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

Implementowanie synchronicznej lub asynchronicznej wersji interfejsu filtru **, a** **nie** obu. Środowisko uruchomieniowe najpierw sprawdza, czy filtr implementuje interfejs asynchroniczny, a jeśli tak, wywołuje to. Jeśli nie, wywołuje metody interfejsu synchronicznego. Jeśli zarówno interfejsy asynchroniczne, jak i synchroniczne są zaimplementowane w jednej klasie, wywoływana jest tylko Metoda async. W przypadku używania klas abstrakcyjnych, takich jak <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , Zastąp tylko metody synchroniczne lub metody asynchroniczne dla każdego typu filtru.

### <a name="built-in-filter-attributes"></a>Wbudowane atrybuty filtru

ASP.NET Core obejmuje wbudowane filtry oparte na atrybutach, które można podklasować i dostosowywać. Na przykład poniższy filtr wynikowy dodaje nagłówek do odpowiedzi:

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

Atrybuty umożliwiają filtrom akceptowanie argumentów, jak pokazano w powyższym przykładzie. Zastosuj `AddHeaderAttribute` do kontrolera lub metodę akcji i określ nazwę i wartość nagłówka http:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

Użyj narzędzia, takiego jak [Narzędzia deweloperskie przeglądarki](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) , aby przeanalizować nagłówki. W obszarze **nagłówki odpowiedzi** `author: Rick Anderson` jest wyświetlany.

Poniższy kod implementuje `ActionFilterAttribute` , że:

* Odczytuje tytuł i nazwę z systemu konfiguracji. W przeciwieństwie do poprzedniego przykładu, poniższy kod nie wymaga dodania parametrów filtru do kodu.
* Dodaje tytuł i nazwę do nagłówka odpowiedzi.

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

Opcje konfiguracji są dostępne z [systemu konfiguracji](xref:fundamentals/configuration/index) przy użyciu [wzorca opcji](xref:fundamentals/configuration/options). Na przykład, z *appsettings.json* pliku:

[!code-json[](filters/3.1sample/FiltersSample/appsettings.json)]

W `StartUp.ConfigureServices` :

* `PositionOptions`Klasa jest dodawana do kontenera usługi z `"Position"` obszarem konfiguracji.
* `MyActionFilterAttribute`Zostanie dodany do kontenera usługi.

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

Poniższy kod przedstawia `PositionOptions` klasę:

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

Poniższy kod stosuje `MyActionFilterAttribute` `Index2` metodę do:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

W obszarze **nagłówki odpowiedzi**, `author: Rick Anderson` i `Editor: Joe Smith` jest wyświetlana po `Sample/Index2` wywołaniu punktu końcowego.

Poniższy kod stosuje `MyActionFilterAttribute` `AddHeaderAttribute` stronę i do Razor strony:

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

Nie można zastosować filtrów do Razor metod obsługi strony. Mogą być stosowane do Razor modelu strony lub globalnie.

Kilka interfejsów filtrów ma odpowiednie atrybuty, które mogą być używane jako klasy bazowe dla implementacji niestandardowych.

Atrybuty filtru:

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a>Zakresy filtrów i kolejność wykonywania

Filtr można dodać do potoku w jednym z trzech *zakresów*:

* Użycie atrybutu w akcji kontrolera. Atrybutów filtru nie można stosować do Razor metod obsługi stron.
* Użycie atrybutu na kontrolerze lub Razor stronie.
* Globalnie dla wszystkich kontrolerów, akcji i Razor stron, jak pokazano w poniższym kodzie:

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a>Domyślna kolejność wykonywania

Jeśli istnieje wiele filtrów dla określonego etapu potoku, zakres określa domyślną kolejność wykonywania filtrowania.  Filtry globalne Otocz filtry klas, które z kolei filtrują metody przestrzenne.

W wyniku zagnieżdżania filtrów, *po* kodzie filtrów działa w odwrotnej kolejności *przed* kodem. Sekwencja filtru:

* *Przed* kodem filtrów globalnych.
  * *Przed* kodem i Razor filtrami stron.
    * Filtr *przed* kodem metody akcji.
    * *Po* kodzie metody akcji filtry.
  * *Po* kodzie kontrolera i Razor filtrów strony.
* Kod *po* filtrach globalnych.
  
Poniższy przykład ilustruje kolejność, w której metody filtrowania są wywoływane dla synchronicznych filtrów akcji.

| Sequence | Zakres filtru | Filter — Metoda |
|:--------:|:------------:|:-------------:|
| 1 | Globalnie | `OnActionExecuting` |
| 2 | Kontroler lub Razor Strona| `OnActionExecuting` |
| 3 | Metoda | `OnActionExecuting` |
| 4 | Metoda | `OnActionExecuted` |
| 5 | Kontroler lub Razor Strona | `OnActionExecuted` |
| 6 | Globalnie | `OnActionExecuted` |

### <a name="controller-level-filters"></a>Filtry na poziomie kontrolera

Każdy kontroler, który dziedziczy z <xref:Microsoft.AspNetCore.Mvc.Controller> klasy podstawowej, obejmuje metody [Controller. OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller. OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*)i [Controller. OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*) 
 `OnActionExecuted` . Te metody:

* Zawiń filtry, które są uruchamiane dla danego działania.
* `OnActionExecuting` jest wywoływana przed dowolnym filtrem akcji.
* `OnActionExecuted` jest wywoływana po wszystkich filtrach akcji.
* `OnActionExecutionAsync` jest wywoływana przed dowolnym filtrem akcji. Kod w filtrze po `next` uruchomieniu po metodzie akcji.

Na przykład, w przykładzie pobierania, `MySampleActionFilter` jest stosowana globalnie podczas uruchamiania.

`TestController`:

* Stosuje `SampleActionFilterAttribute` ( `[SampleActionFilter]` ) do `FilterTest2` akcji.
* Przesłania `OnActionExecuting` i `OnActionExecuted` .

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

[!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

Nawigowanie w celu `https://localhost:5001/Test/FilterTest2` uruchomienia następującego kodu:

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

Filtry na poziomie kontrolera ustawiają Właściwość [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) na `int.MinValue` . Filtrów na poziomie kontrolera **nie** można ustawić do uruchomienia po zastosowaniu filtrów do metod. Klauzula Order została omówiona w następnej sekcji.

Aby uzyskać Razor strony, zobacz [implementowanie Razor filtrów stron przez zastępowanie metod filtrowania](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).

### <a name="overriding-the-default-order"></a>Zastępowanie kolejności domyślnej

Domyślna sekwencja wykonywania może zostać przesłonięta przez implementację <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> . `IOrderedFilter` uwidacznia <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> Właściwość, która ma pierwszeństwo przed zakresem w celu określenia kolejności wykonywania. Filtr o niższej `Order` wartości:

* Uruchamia *przed* kodem przed filtrem o wyższej wartości `Order` .
* Uruchamia *po* kodzie po tym filtrze o wyższej `Order` wartości.

`Order`Właściwość jest ustawiona z parametrem konstruktora:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

Należy wziąć pod uwagę dwa filtry akcji na następującym kontrolerze:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

Filtr globalny zostanie dodany w `StartUp.ConfigureServices` :

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

3 filtry działają w następującej kolejności:

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MyAction2FilterAttribute.OnResultExecuting`
  * `MySampleActionFilter.OnActionExecuted`
* `Test2Controller.OnActionExecuted`

`Order`Właściwość przesłania zakres podczas określania kolejności, w której są uruchamiane filtry. Filtry są sortowane najpierw według kolejności, a następnie zakres jest używany do przerwania powiązań. Wszystkie wbudowane filtry implementują `IOrderedFilter` i ustawiają `Order` wartość domyślną 0. Jak wspomniano wcześniej, filtry na poziomie kontrolera [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) ustawiają Właściwość Order `int.MinValue` dla filtrów wbudowanych, zakres określa kolejność, chyba że `Order` jest ustawiona na wartość różną od zera.

W poprzednim kodzie `MySampleActionFilter` ma zakres globalny, dlatego działa wcześniej `MyAction2FilterAttribute` , który ma zakres kontrolera. Aby `MyAction2FilterAttribute` najpierw wykonać przebieg, ustaw kolejność na `int.MinValue` :

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

Aby najpierw uruchomić filtr globalny `MySampleActionFilter` , ustaw opcję `Order` na `int.MinValue` :

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a>Anulowanie i krótkie obwody

Potok filtru może być skrócony przez ustawienie <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> właściwości <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parametru dostarczonego do metody filtra. Na przykład poniższy filtr zasobów uniemożliwia wykonanie pozostałej części potoku:

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

W poniższym kodzie, zarówno, `ShortCircuitingResourceFilter` jak i jako `AddHeader` element docelowy filtru `SomeResource` metodę akcji. `ShortCircuitingResourceFilter`:

* Uruchamiany jako pierwszy, ponieważ jest filtrem zasobów i `AddHeader` jest filtrem akcji.
* Krótkie obwody pozostała część potoku.

W związku z tym `AddHeader` Filtr nigdy nie jest uruchamiany dla `SomeResource` akcji. Takie zachowanie będzie takie samo, jeśli oba filtry zostały zastosowane na poziomie metody akcji, pod warunkiem, że `ShortCircuitingResourceFilter` uruchomiono pierwsze. `ShortCircuitingResourceFilter`Przebiega najpierw ze względu na jego typ filtru lub jawne użycie `Order` właściwości.

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a>Wstrzykiwanie zależności

Filtry można dodawać według typu lub według wystąpienia. W przypadku dodania wystąpienia to wystąpienie jest używane dla każdego żądania. Jeśli typ zostanie dodany, jego typ jest aktywowany. Filtr aktywowany przez typ oznacza:

* Wystąpienie jest tworzone dla każdego żądania.
* Wszystkie zależności konstruktora są wypełniane przez [iniekcję zależności](xref:fundamentals/dependency-injection) (di).

Filtry zaimplementowane jako atrybuty i dodawane bezpośrednio do klas kontrolera lub metod akcji nie mogą mieć zależności konstruktorów udostępnianych przez [iniekcję zależności](xref:fundamentals/dependency-injection) (di). Zależności konstruktora nie mogą być dostarczone przez DI, ponieważ:

* Atrybuty muszą mieć podane parametry konstruktora, w których są stosowane. 
* Jest to ograniczenie działania atrybutów.

Następujące filtry obsługują zależności konstruktora dostarczone z elementów DI:

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> zaimplementowane dla atrybutu.

Powyższe filtry można zastosować do kontrolera lub metody akcji:

Rejestratory są dostępne z programu DI. Należy jednak unikać tworzenia i używania filtrów wyłącznie w celach rejestrowania. [Wbudowane rejestrowanie struktury](xref:fundamentals/logging/index) zazwyczaj zapewnia, co jest potrzebne do rejestrowania. Rejestrowanie dodane do filtrów:

* Należy skoncentrować się na problemach z domeną biznesową lub działaniu specyficznym dla filtra.
* **Nie** należy rejestrować akcji ani innych zdarzeń struktury. Wbudowane filtry rejestrują akcje i zdarzenia struktury.

### <a name="servicefilterattribute"></a>Servicefilterattribute

Typy implementacji filtru usługi są zarejestrowane w `ConfigureServices` . <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>Pobiera wystąpienie filtru z di.

Poniższy kod przedstawia `AddHeaderResultServiceFilter` :

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

W poniższym kodzie, `AddHeaderResultServiceFilter` jest dodawany do kontenera di:

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

W poniższym kodzie `ServiceFilter` atrybut Pobiera wystąpienie `AddHeaderResultServiceFilter` filtru z di:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

W przypadku korzystania `ServiceFilterAttribute` z ustawienia [servicefilterattribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):

* Zapewnia wskazówkę, że wystąpienie filtru *może* być ponownie używane poza zakresem żądania, który został utworzony w ramach. Środowisko uruchomieniowe ASP.NET Core nie gwarantuje:

  * Zostanie utworzone pojedyncze wystąpienie filtru.
  * Filtr nie zostanie ponownie żądany z kontenera DI w pewnym momencie.

* Nie powinien być używany z filtrem, który zależy od usług z okresem istnienia innym niż pojedynczy.

 <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> . `IFilterFactory` uwidacznia <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodę tworzenia <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> wystąpienia. `CreateInstance` Ładuje określony typ z DI.

### <a name="typefilterattribute"></a>TypeFilterAttribute

<xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> jest podobny do <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> , ale jego typ nie jest rozpoznawany bezpośrednio w kontenerze di. Tworzy wystąpienie tego typu przy użyciu <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> .

Ponieważ `TypeFilterAttribute` typy nie są rozpoznawane bezpośrednio w kontenerze di:

* Typy, do których istnieją odwołania, `TypeFilterAttribute` nie muszą być zarejestrowane przy użyciu di kontenera.  Ich zależności są spełnione przez kontener DI.
* `TypeFilterAttribute` Opcjonalnie można zaakceptować argumenty konstruktora dla tego typu.

W przypadku korzystania `TypeFilterAttribute` z programu ustaw wartość [TypeFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):
* Zapewnia wskazówkę, że wystąpienie filtru *może* być ponownie używane poza zakresem żądania, który został utworzony w ramach. Środowisko uruchomieniowe ASP.NET Core nie zapewnia żadnych gwarancji, że zostanie utworzone pojedyncze wystąpienie filtru.

* Nie powinien być używany z filtrem, który zależy od usług z okresem istnienia innym niż pojedynczy.

Poniższy przykład pokazuje, jak przekazywać argumenty do typu przy użyciu `TypeFilterAttribute` :

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a>Filtry autoryzacji

Filtry autoryzacji:

* Są pierwszymi filtrami uruchamianymi w potoku filtru.
* Kontrola dostępu do metod akcji.
* Ma metodę Before, ale nie po metodzie.

Niestandardowe filtry autoryzacji wymagają niestandardowej struktury autoryzacji. Preferuj skonfigurowanie zasad autoryzacji lub zapisanie niestandardowych zasad autoryzacji przez zapisanie filtru niestandardowego. Wbudowany filtr autoryzacji:

* Wywołuje system autoryzacji.
* Nie zezwala na żądania.

**Nie** zgłaszaj wyjątków w ramach filtrów autoryzacji:

* Wyjątek nie zostanie obsłużony.
* Filtry wyjątków nie będą obsługiwać wyjątku.

Rozważ wygenerowanie wyzwania w przypadku wystąpienia wyjątku w filtrze autoryzacji.

Dowiedz się więcej o [autoryzacji](xref:security/authorization/introduction).

## <a name="resource-filters"></a>Filtry zasobów

Filtry zasobów:

* Zaimplementuj <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> interfejs lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> .
* Wykonanie powoduje Zawijanie większości potoku filtru.
* Tylko [filtry autoryzacji](#authorization-filters) są uruchamiane przed filtrami zasobów.

Filtry zasobów są przydatne do krótkiego obwodu większości potoku. Na przykład filtr buforowania może uniknąć pozostałej części potoku w pamięci podręcznej.

Przykłady filtru zasobów:

* [Filtr zasobów o krótkim obwodzie](#short-circuiting-resource-filter) pokazano wcześniej.
* [DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):

  * Uniemożliwia powiązanie modelu z dostępem do danych formularza.
  * Służy do przekazywania dużych plików w celu uniemożliwienia odczytywania danych formularza do pamięci.

## <a name="action-filters"></a>Filtry akcji

Filtry akcji **nie** mają zastosowania do Razor stron. Razor Strony obsługują <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> i <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> . Aby uzyskać więcej informacji, zobacz [metody filtrowania dla Razor stron](xref:razor-pages/filter).

Filtry akcji:

* Zaimplementuj <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> interfejs lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> .
* Ich wykonanie otacza wykonywanie metod akcji.

Poniższy kod przedstawia przykładowy filtr akcji:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext>Zawiera następujące właściwości:

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> — umożliwia odczytywanie danych wejściowych do metody akcji.
* <xref:Microsoft.AspNetCore.Mvc.Controller> -Włącza manipulowanie wystąpieniem kontrolera.
* <xref:System.Web.Mvc.ActionExecutingContext.Result> -Ustawianie `Result` wykonywania krótkich obwodów metody akcji i kolejnych filtrów akcji.

Zgłaszanie wyjątku w metodzie akcji:

* Zapobiega uruchamianiu kolejnych filtrów.
* W przeciwieństwie do ustawienia `Result` , jest traktowany jako błąd, a nie pomyślny wynik.

<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext>Zawiera `Controller` i oraz `Result` oraz następujące właściwości:

* <xref:System.Web.Mvc.ActionExecutedContext.Canceled> -True, jeśli wykonywanie akcji zostało skrócone przez inny filtr.
* <xref:System.Web.Mvc.ActionExecutedContext.Exception> -O wartości innej niż null, jeśli filtr akcji lub poprzednio uruchomionego wywołał wyjątek. Ustawienie tej właściwości na wartość null:

  * Skutecznie obsługuje wyjątek.
  * `Result` jest wykonywany tak, jakby został zwrócony z metody akcji.

Dla `IAsyncActionFilter` , wywołanie <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> :

* Wykonuje wszystkie kolejne filtry akcji i metodę akcji.
* Zwraca wartość `ActionExecutedContext`.

Do krótkiego obwodu, przypisz <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> do wystąpienia wynikowego i nie wywołuj `next` ( `ActionExecutionDelegate` ).

Struktura zawiera abstrakcyjny <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , który może być podklasą.

`OnActionExecuting`Filtr akcji może służyć do:

* Zweryfikuj stan modelu.
* Zwróć błąd, jeśli stan jest nieprawidłowy.

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

> [!NOTE]
> Kontrolery z adnotacją z `[ApiController]` atrybutem automatycznie weryfikują stan modelu i zwracają odpowiedź 400. Aby uzyskać więcej informacji, zobacz [Automatyczne HTTP 400 odpowiedzi](xref:web-api/index#automatic-http-400-responses).

`OnActionExecuted`Metoda jest uruchamiana po metodzie akcji:

* I mogą przeglądać wyniki akcji przez właściwość i manipulować nimi <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> .
* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> jest ustawiona na wartość true, jeśli wykonywanie akcji zostało skrócone przez inny filtr.
* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> ma ustawioną wartość różną od null, jeśli akcja lub kolejny filtr akcji wywołał wyjątek. Ustawienie `Exception` na wartość null:

  * Efektywnie obsługuje wyjątek.
  * `ActionExecutedContext.Result` jest wykonywany tak, jakby został zwrócony normalnie z metody akcji.

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a>Filtry wyjątków

Filtry wyjątków:

* Implementowanie <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter> .
* Może służyć do implementowania typowych zasad obsługi błędów.

Następujący przykładowy filtr wyjątku używa niestandardowego widoku błędów, aby wyświetlić szczegóły dotyczące wyjątków występujących podczas opracowywania aplikacji:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

Następujący kod testuje filtr wyjątku:

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

Filtry wyjątków:

* Nie mam zdarzeń przed i po.
* Implementowanie <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*> .
* Obsługuj Nieobsłużone wyjątki występujące podczas Razor tworzenia strony lub kontrolera, [powiązania modelu](xref:mvc/models/model-binding), filtrów akcji lub metod akcji.
* **Nie** należy przechwytywać wyjątków występujących w filtrach zasobów, filtrach wyników ani wykonywaniu wyniku MVC.

Aby obsłużyć wyjątek, ustaw <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> Właściwość na `true` lub Zapisz odpowiedź. Spowoduje to zatrzymanie propagacji wyjątku. Filtr wyjątku nie może przekształcić wyjątku w "powodzenie". Można to zrobić tylko dla filtru akcji.

Filtry wyjątków:

* Są dobre dla wyjątków zalewkowania występujących w ramach akcji.
* Nie są tak elastyczne jak błędy obsługi oprogramowania pośredniczącego.

Preferuj oprogramowanie pośredniczące dla obsługi wyjątków. Użyj filtrów wyjątków tylko w przypadku, gdy obsługa błędów *różni* się w zależności od tego, która metoda akcji jest wywoływana. Na przykład aplikacja może mieć metody akcji zarówno dla punktów końcowych interfejsu API, jak i dla widoków/HTML. Punkty końcowe interfejsu API mogą zwracać informacje o błędach jako dane JSON, podczas gdy akcje oparte na widoku mogą zwrócić stronę błędu jako kod HTML.

## <a name="result-filters"></a>Filtry wyników

Filtry wyników:

* Zaimplementuj interfejs:
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter>
* Ich wykonanie otacza wykonywanie wyników akcji.

### <a name="iresultfilter-and-iasyncresultfilter"></a>IResultFilter i IAsyncResultFilter

Poniższy kod pokazuje filtr wynikowy, który dodaje nagłówek HTTP:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

Rodzaj wykonywanego wyniku zależy od akcji. Akcja zwracająca widok obejmuje wszystkie procesy przetwarzania Razor w ramach <xref:Microsoft.AspNetCore.Mvc.ViewResult> wykonywanej operacji. Metoda interfejsu API może wykonywać pewne serializacji w ramach wykonywania wyniku. Dowiedz się więcej o [wynikach akcji](xref:mvc/controllers/actions).

Filtry wynikowe są wykonywane tylko wtedy, gdy akcja lub filtr akcji generuje wynik akcji. Filtry wynikowe nie są wykonywane, gdy:

* Filtr autoryzacji lub filtr zasobów jest krótki obwody potoku.
* Filtr wyjątku obsługuje wyjątek przez wygenerowanie wyniku akcji.

<xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName>Metoda może skrócić wykonywanie wyniku akcji i kolejnych filtrów wynikowych przez ustawienie wartości <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> `true` . Zapisuj w obiekcie Response podczas krótkiego obwodu, aby uniknąć generowania pustej odpowiedzi. Zgłaszanie wyjątku w `IResultFilter.OnResultExecuting` :

* Zapobiega wykonywaniu wyniku akcji i kolejnych filtrów.
* Jest traktowany jako niepowodzenie, a nie wynikowy pomyślnie.

Po <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> uruchomieniu metody odpowiedź prawdopodobnie została już wysłana do klienta. Jeśli odpowiedź została już wysłana do klienta, nie można jej zmienić.

`ResultExecutedContext.Canceled` jest ustawiona na, `true` Jeśli wykonywanie wyniku akcji zostało skrócone przez inny filtr.

`ResultExecutedContext.Exception` ma ustawioną wartość różną od null, jeśli wynik akcji lub kolejny filtr wyników wywołał wyjątek. Ustawienie `Exception` wartości null skutecznie obsługuje wyjątek i uniemożliwia ponowne zgłoszenie wyjątku w dalszej części potoku. Nie istnieje niezawodny sposób zapisu danych do odpowiedzi podczas obsługi wyjątku w filtrze wynikowym. Jeśli nagłówki zostały opróżnione do klienta, gdy wynikiem akcji zgłasza wyjątek, nie istnieje niezawodny mechanizm wysyłania kodu błędu.

Dla <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> , wywołanie `await next` na <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> wykonuje wszystkie kolejne filtry wynikowe i wynik działania. Do krótkiego obwodu Ustaw [ResultExecutingContext. Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) na `true` i nie wywołuj `ResultExecutionDelegate` :

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

Struktura zawiera abstrakcyjny `ResultFilterAttribute` , który może być podklasą. Przedstawiona wcześniej Klasa [Addheaderattribute](#add-header-attribute) jest przykładem atrybutu filtru wynikowego.

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a>IAlwaysRunResultFilter i IAsyncAlwaysRunResultFilter

<xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter>Interfejsy i <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> deklarują <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementację, która jest uruchamiana dla wszystkich wyników akcji. Obejmuje to wyniki akcji generowane przez:

* Filtry autoryzacji i filtry zasobów, które są skracane.
* Filtry wyjątków.

Na przykład następujący filtr jest zawsze uruchamiany i ustawia wynik akcji ( <xref:Microsoft.AspNetCore.Mvc.ObjectResult> ) z kodem stanu *jednostki nieprzetwarzanych 422* , gdy negocjowanie zawartości kończy się niepowodzeniem:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a>IFilterFactory

<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> . W związku z tym `IFilterFactory` wystąpienie może być używane jako `IFilterMetadata` wystąpienie w dowolnym miejscu w potoku filtru. Gdy środowisko uruchomieniowe przygotowuje się do wywołania filtru, próbuje rzutować go na `IFilterFactory` . Jeśli rzutowanie powiedzie się, <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> Metoda zostanie wywołana w celu utworzenia `IFilterMetadata` wystąpienia, które jest wywoływane. Zapewnia to elastyczny projekt, ponieważ dokładny potok filtru nie musi być ustawiony jawnie podczas uruchamiania aplikacji.

`IFilterFactory.IsReusable`:

* Jest wskazówką dla fabryki, że wystąpienie filtru utworzone przez fabrykę może być ponownie używane poza zakresem żądania, który został utworzony w ramach.
* **Nie** powinna być używana z filtrem, który zależy od usług z okresem istnienia innym niż singleton.

Środowisko uruchomieniowe ASP.NET Core nie gwarantuje:

Oznacza, że zostanie utworzone pojedyncze wystąpienie filtru.
* Filtr nie zostanie ponownie żądany z kontenera DI w pewnym momencie.

[!WARNING] Skonfigurować tylko `IFilterFactory.IsReusable` do zwrócenia, `true` Jeśli źródło filtrów nie jest niejednoznaczne, filtry są bezstanowe i bezpieczne do użycia w wielu żądaniach HTTP. Na przykład nie należy zwracać filtrów z funkcji DI, które są rejestrowane jako zakresy lub przejściowe, jeśli `IFilterFactory.IsReusable` zwraca `true`

`IFilterFactory` można zaimplementować przy użyciu niestandardowych implementacji atrybutów jako inne podejście do tworzenia filtrów:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

Filtr jest stosowany w następującym kodzie:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

Przetestuj poprzedni kod, uruchamiając [przykład pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):

* Wywołaj narzędzia deweloperskie F12.
* Przejdź do adresu `https://localhost:5001/Sample/HeaderWithFactory`.

Narzędzia programistyczne F12 wyświetlają następujące nagłówki odpowiedzi dodane przez przykładowy kod:

* **autor:**`Rick Anderson`
* **globaladdheader:**`Result filter added to MvcOptions.Filters`
* **wewnętrzny:**`My header`

Poprzedni kod tworzy nagłówek **Internal:** `My header` Response.

### <a name="ifilterfactory-implemented-on-an-attribute"></a>IFilterFactory zaimplementowane dla atrybutu

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

Filtry, które implementują `IFilterFactory` są przydatne dla filtrów, które:

* Nie wymagaj parametrów przekazujących.
* Mają zależności konstruktora, które muszą być wypełnione przez DI.

<xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> . `IFilterFactory` uwidacznia <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodę tworzenia <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> wystąpienia. `CreateInstance` Ładuje określony typ z kontenera usług (DI).

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

Poniższy kod przedstawia trzy podejścia do zastosowania `[SampleActionFilter]` :

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

W poprzednim kodzie dekorowania nazwy metodę with `[SampleActionFilter]` jest preferowanym podejściem do zastosowania `SampleActionFilter` .

## <a name="using-middleware-in-the-filter-pipeline"></a>Używanie oprogramowania pośredniczącego w potoku filtru

Filtry zasobów działają podobnie jak [oprogramowanie pośredniczące](xref:fundamentals/middleware/index) w celu obsłużenia wykonywania wszystkich elementów, które są późniejsze w potoku. Ale filtry różnią się od oprogramowania pośredniczącego w tym, że są częścią środowiska uruchomieniowego, co oznacza, że ma dostęp do kontekstu i konstrukcji.

Aby użyć oprogramowania pośredniczącego jako filtru, Utwórz typ z `Configure` metodą, która określa oprogramowanie pośredniczące, które ma zostać dodane do potoku filtru. W poniższym przykładzie jest wykorzystywane oprogramowanie pośredniczące lokalizacyjne do ustalenia bieżącej kultury dla żądania:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

Użyj, <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> Aby uruchomić oprogramowanie pośredniczące:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

Filtry oprogramowania pośredniczącego są uruchamiane na tym samym etapie potoku filtru jako filtry zasobów przed powiązaniem modelu i po pozostałej części potoku.

## <a name="next-actions"></a>Następne akcje

* Zobacz [metody filtrowania dla Razor stron](xref:razor-pages/filter).
* Aby eksperymentować z filtrami, należy [pobrać, przetestować i zmodyfikować przykład usługi GitHub](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Kirka Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tomasz Dykstra](https://github.com/tdykstra/)i [Steve Smith](https://ardalis.com/)

*Filtry* w ASP.NET Core umożliwiają uruchamianie kodu przed określonymi etapami w potoku przetwarzania żądań lub po nich.

Filtry wbudowane obsługują zadania takie jak:

* Autoryzacja (zapobieganie dostępowi do zasobów, dla których użytkownik nie jest autoryzowany).
* Buforowanie odpowiedzi (krótkie obwody potoku żądania w celu zwrócenia buforowanej odpowiedzi).

Filtry niestandardowe mogą być tworzone w celu obsłużenia krzyżowego rozcinania. Przykłady zagadnień związanych z rozcinaniem obejmują obsługę błędów, buforowanie, konfigurację, autoryzację i rejestrowanie.  Filtry unikają duplikowania kodu. Na przykład filtr wyjątków obsługujący błędy może skonsolidować obsługę błędów.

Ten dokument ma zastosowanie do Razor stron, kontrolerów interfejsu API i kontrolerów z widokami.

[Wyświetl lub Pobierz przykład](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([jak pobrać](xref:index#how-to-download-a-sample)).

## <a name="how-filters-work"></a>Jak działają filtry

Filtry są uruchamiane w *potoku wywołania akcji ASP.NET Core*, czasami określane jako *potok filtru*.  Potok filtru jest uruchamiany po ASP.NET Core wybiera akcję do wykonania.

![Żądanie jest przetwarzane przez inne oprogramowanie pośredniczące, kierowanie oprogramowania pośredniczącego, wybór akcji i potok wywołania akcji ASP.NET Core. Przetwarzanie żądań jest kontynuowane przez wybór akcji, kierowanie oprogramowania pośredniczącego i różnych innych programów pośredniczących przed wysłaniem odpowiedzi do klienta.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a>Typy filtrów

Każdy typ filtru jest wykonywany na innym etapie w potoku filtru:

* [Filtry autoryzacji](#authorization-filters) są uruchamiane jako pierwsze i są używane do określenia, czy użytkownik jest autoryzowany do żądania. Filtry autoryzacji skracają obwód w przypadku, gdy żądanie jest nieautoryzowane.

* [Filtry zasobów](#resource-filters):

  * Uruchom po autoryzacji.  
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> można uruchomić kod przed resztą potoku filtru. Na przykład program `OnResourceExecuting` może uruchomić kod przed powiązaniem modelu.
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> można uruchomić kod po zakończeniu reszty potoku.

* [Filtry akcji](#action-filters) mogą uruchomić kod bezpośrednio przed i po wywołaniu pojedynczej metody akcji. Mogą one służyć do manipulowania argumentami przekazaną do akcji i wynik zwrócony z akcji. Filtry akcji **nie** są obsługiwane na Razor stronach.

* [Filtry wyjątków](#exception-filters) są używane do stosowania zasad globalnych do nieobsłużonych wyjątków, które wystąpiły przed zapisem w treści odpowiedzi.

* [Filtry wynikowe](#result-filters) mogą uruchamiać kod bezpośrednio przed i po wykonaniu poszczególnych wyników akcji. Są one uruchamiane tylko wtedy, gdy metoda akcji została wykonana pomyślnie. Są one przydatne dla logiki, która musi być w trakcie wyświetlania lub wykonywania w programie formatującego.

Na poniższym diagramie przedstawiono sposób, w jaki typy filtrów współdziałają w potoku filtru.

![Żądanie jest przetwarzane przez filtry autoryzacji, filtry zasobów, powiązania modelu, filtry akcji, wykonywanie akcji i konwersję wyników akcji, filtry wyjątków, filtry wynikowe i wykonywanie wyniku. W ten sposób żądanie jest przetwarzane tylko przez filtry wyników i filtry zasobów przed wysłaniem odpowiedzi do klienta.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a>Implementacja

Filtry obsługują implementacje synchroniczne i asynchroniczne za pomocą różnych definicji interfejsu.

Filtry synchroniczne mogą uruchamiać kod przed ( `On-Stage-Executing` ) i po nim ( `On-Stage-Executed` ) ich etap potoku. Na przykład, `OnActionExecuting` jest wywoływana przed wywołaniem metody akcji. `OnActionExecuted` jest wywoływana po powrocie metody akcji.

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

Filtry asynchroniczne definiują `On-Stage-ExecutionAsync` metodę:

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

W poprzednim kodzie, `SampleAsyncActionFilter` ma <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> ( `next` ), który wykonuje metodę akcji.  Każda z tych `On-Stage-ExecutionAsync` metod przyjmuje `FilterType-ExecutionDelegate` etap potoku filtru.

### <a name="multiple-filter-stages"></a>Wiele etapów filtrowania

Interfejsy dla wielu etapów filtru można zaimplementować w pojedynczej klasie. Na przykład <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> Klasa implementuje `IActionFilter` , `IResultFilter` i ich odpowiedniki asynchroniczne.

Implementowanie synchronicznej lub asynchronicznej wersji interfejsu filtru **, a** **nie** obu. Środowisko uruchomieniowe najpierw sprawdza, czy filtr implementuje interfejs asynchroniczny, a jeśli tak, wywołuje to. Jeśli nie, wywołuje metody interfejsu synchronicznego. Jeśli zarówno interfejsy asynchroniczne, jak i synchroniczne są zaimplementowane w jednej klasie, wywoływana jest tylko Metoda async. W przypadku używania klas abstrakcyjnych, takich jak <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> zastąpienie tylko metod synchronicznych lub metodę asynchroniczną dla każdego typu filtru.

### <a name="built-in-filter-attributes"></a>Wbudowane atrybuty filtru

ASP.NET Core obejmuje wbudowane filtry oparte na atrybutach, które można podklasować i dostosowywać. Na przykład poniższy filtr wynikowy dodaje nagłówek do odpowiedzi:

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

Atrybuty umożliwiają filtrom akceptowanie argumentów, jak pokazano w powyższym przykładzie. Zastosuj `AddHeaderAttribute` do kontrolera lub metodę akcji i określ nazwę i wartość nagłówka http:

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

Kilka interfejsów filtrów ma odpowiednie atrybuty, które mogą być używane jako klasy bazowe dla implementacji niestandardowych.

Atrybuty filtru:

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a>Zakresy filtrów i kolejność wykonywania

Filtr można dodać do potoku w jednym z trzech *zakresów*:

* Użycie atrybutu w akcji.
* Używanie atrybutu na kontrolerze.
* Globalnie dla wszystkich kontrolerów i akcji, jak pokazano w poniższym kodzie:

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

Poprzedni kod dodaje trzy filtry globalnie przy użyciu kolekcji [MvcOptions. filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) .

### <a name="default-order-of-execution"></a>Domyślna kolejność wykonywania

Jeśli istnieje wiele filtrów tego *samego typu*, zakres określa domyślną kolejność wykonywania filtrowania.  Filtry globalne Otocz filtry klas. Filtry klas Otocz filtry metod.

W wyniku zagnieżdżania filtrów, *po* kodzie filtrów działa w odwrotnej kolejności *przed* kodem. Sekwencja filtru:

* *Przed* kodem filtrów globalnych.
  * *Przed* kodem filtrów kontrolera.
    * Filtr *przed* kodem metody akcji.
    * *Po* kodzie metody akcji filtry.
  * *Po* kodzie filtrów kontrolera.
* Kod *po* filtrach globalnych.
  
Poniższy przykład ilustruje kolejność, w której metody filtrowania są wywoływane dla synchronicznych filtrów akcji.

| Sequence | Zakres filtru | Filter — Metoda |
|:--------:|:------------:|:-------------:|
| 1 | Globalnie | `OnActionExecuting` |
| 2 | Kontroler | `OnActionExecuting` |
| 3 | Metoda | `OnActionExecuting` |
| 4 | Metoda | `OnActionExecuted` |
| 5 | Kontroler | `OnActionExecuted` |
| 6 | Globalnie | `OnActionExecuted` |

Ta sekwencja pokazuje:

* Filtr metody jest zagnieżdżony w obrębie filtru kontrolera.
* Filtr kontrolera jest zagnieżdżony w obrębie filtru globalnego.

### <a name="controller-and-no-locrazor-page-level-filters"></a>Filtry na Razor poziomie kontrolera i strony

Każdy kontroler, który dziedziczy z <xref:Microsoft.AspNetCore.Mvc.Controller> klasy podstawowej, obejmuje metody [Controller. OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller. OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*)i [Controller. OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*) 
 `OnActionExecuted` . Te metody:

* Zawiń filtry, które są uruchamiane dla danego działania.
* `OnActionExecuting` jest wywoływana przed dowolnym filtrem akcji.
* `OnActionExecuted` jest wywoływana po wszystkich filtrach akcji.
* `OnActionExecutionAsync` jest wywoływana przed dowolnym filtrem akcji. Kod w filtrze po `next` uruchomieniu po metodzie akcji.

Na przykład, w przykładzie pobierania, `MySampleActionFilter` jest stosowana globalnie podczas uruchamiania.

`TestController`:

* Stosuje `SampleActionFilterAttribute` ( `[SampleActionFilter]` ) do `FilterTest2` akcji.
* Przesłania `OnActionExecuting` i `OnActionExecuted` .

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

Nawigowanie w celu `https://localhost:5001/Test/FilterTest2` uruchomienia następującego kodu:

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

Aby uzyskać Razor strony, zobacz [implementowanie Razor filtrów stron przez zastępowanie metod filtrowania](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).

### <a name="overriding-the-default-order"></a>Zastępowanie kolejności domyślnej

Domyślna sekwencja wykonywania może zostać przesłonięta przez implementację <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> . `IOrderedFilter` uwidacznia <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> Właściwość, która ma pierwszeństwo przed zakresem w celu określenia kolejności wykonywania. Filtr o niższej `Order` wartości:

* Uruchamia *przed* kodem przed filtrem o wyższej wartości `Order` .
* Uruchamia *po* kodzie po tym filtrze o wyższej `Order` wartości.

`Order`Właściwość można ustawić przy użyciu parametru konstruktora:

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

Należy wziąć pod uwagę te same 3 filtry akcji, które przedstawiono w powyższym przykładzie. Jeśli `Order` Właściwość kontrolera i filtry globalne zostały odpowiednio ustawione na 1 i 2, kolejność wykonywania zostanie odwrócona.

| Sequence | Zakres filtru | `Order` wartość | Filter — Metoda |
|:--------:|:------------:|:-----------------:|:-------------:|
| 1 | Metoda | 0 | `OnActionExecuting` |
| 2 | Kontroler | 1  | `OnActionExecuting` |
| 3 | Globalnie | 2  | `OnActionExecuting` |
| 4 | Globalnie | 2  | `OnActionExecuted` |
| 5 | Kontroler | 1  | `OnActionExecuted` |
| 6 | Metoda | 0  | `OnActionExecuted` |

`Order`Właściwość przesłania zakres podczas określania kolejności, w której są uruchamiane filtry. Filtry są sortowane najpierw według kolejności, a następnie zakres jest używany do przerwania powiązań. Wszystkie wbudowane filtry implementują `IOrderedFilter` i ustawiają `Order` wartość domyślną 0. W przypadku filtrów wbudowanych zakres określa kolejność, chyba że `Order` jest ustawiona na wartość różną od zera.

## <a name="cancellation-and-short-circuiting"></a>Anulowanie i krótkie obwody

Potok filtru może być skrócony przez ustawienie <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> właściwości <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parametru dostarczonego do metody filtra. Na przykład poniższy filtr zasobów uniemożliwia wykonanie pozostałej części potoku:

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

W poniższym kodzie, zarówno, `ShortCircuitingResourceFilter` jak i jako `AddHeader` element docelowy filtru `SomeResource` metodę akcji. `ShortCircuitingResourceFilter`:

* Uruchamiany jako pierwszy, ponieważ jest filtrem zasobów i `AddHeader` jest filtrem akcji.
* Krótkie obwody pozostała część potoku.

W związku z tym `AddHeader` Filtr nigdy nie jest uruchamiany dla `SomeResource` akcji. Takie zachowanie będzie takie samo, jeśli oba filtry zostały zastosowane na poziomie metody akcji, pod warunkiem, że `ShortCircuitingResourceFilter` uruchomiono pierwsze. `ShortCircuitingResourceFilter`Przebiega najpierw ze względu na jego typ filtru lub jawne użycie `Order` właściwości.

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a>Wstrzykiwanie zależności

Filtry można dodawać według typu lub według wystąpienia. W przypadku dodania wystąpienia to wystąpienie jest używane dla każdego żądania. Jeśli typ zostanie dodany, jego typ jest aktywowany. Filtr aktywowany przez typ oznacza:

* Wystąpienie jest tworzone dla każdego żądania.
* Wszystkie zależności konstruktora są wypełniane przez [iniekcję zależności](xref:fundamentals/dependency-injection) (di).

Filtry zaimplementowane jako atrybuty i dodawane bezpośrednio do klas kontrolera lub metod akcji nie mogą mieć zależności konstruktorów udostępnianych przez [iniekcję zależności](xref:fundamentals/dependency-injection) (di). Zależności konstruktora nie mogą być dostarczone przez DI, ponieważ:

* Atrybuty muszą mieć podane parametry konstruktora, w których są stosowane. 
* Jest to ograniczenie działania atrybutów.

Następujące filtry obsługują zależności konstruktora dostarczone z elementów DI:

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> zaimplementowane dla atrybutu.

Powyższe filtry można zastosować do kontrolera lub metody akcji:

Rejestratory są dostępne z programu DI. Należy jednak unikać tworzenia i używania filtrów wyłącznie w celach rejestrowania. [Wbudowane rejestrowanie struktury](xref:fundamentals/logging/index) zazwyczaj zapewnia, co jest potrzebne do rejestrowania. Rejestrowanie dodane do filtrów:

* Należy skoncentrować się na problemach z domeną biznesową lub działaniu specyficznym dla filtra.
* **Nie** należy rejestrować akcji ani innych zdarzeń struktury. Wbudowane filtry akcje dziennika i zdarzenia struktury.

### <a name="servicefilterattribute"></a>Servicefilterattribute

Typy implementacji filtru usługi są zarejestrowane w `ConfigureServices` . <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>Pobiera wystąpienie filtru z di.

Poniższy kod przedstawia `AddHeaderResultServiceFilter` :

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

W poniższym kodzie, `AddHeaderResultServiceFilter` jest dodawany do kontenera di:

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

W poniższym kodzie `ServiceFilter` atrybut Pobiera wystąpienie `AddHeaderResultServiceFilter` filtru z di:

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

W przypadku korzystania `ServiceFilterAttribute` z ustawienia [servicefilterattribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):

* Zapewnia wskazówkę, że wystąpienie filtru *może* być ponownie używane poza zakresem żądania, który został utworzony w ramach. Środowisko uruchomieniowe ASP.NET Core nie gwarantuje:

  * Zostanie utworzone pojedyncze wystąpienie filtru.
  * Filtr nie zostanie ponownie żądany z kontenera DI w pewnym momencie.

* Nie powinien być używany z filtrem, który zależy od usług z okresem istnienia innym niż pojedynczy.

 <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> . `IFilterFactory` uwidacznia <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodę tworzenia <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> wystąpienia. `CreateInstance` Ładuje określony typ z DI.

### <a name="typefilterattribute"></a>TypeFilterAttribute

<xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> jest podobny do <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> , ale jego typ nie jest rozpoznawany bezpośrednio w kontenerze di. Tworzy wystąpienie tego typu przy użyciu <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> .

Ponieważ `TypeFilterAttribute` typy nie są rozpoznawane bezpośrednio w kontenerze di:

* Typy, do których istnieją odwołania, `TypeFilterAttribute` nie muszą być zarejestrowane przy użyciu di kontenera.  Ich zależności są spełnione przez kontener DI.
* `TypeFilterAttribute` Opcjonalnie można zaakceptować argumenty konstruktora dla tego typu.

W przypadku korzystania `TypeFilterAttribute` z programu ustaw wartość [TypeFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):
* Zapewnia wskazówkę, że wystąpienie filtru *może* być ponownie używane poza zakresem żądania, który został utworzony w ramach. Środowisko uruchomieniowe ASP.NET Core nie zapewnia żadnych gwarancji, że zostanie utworzone pojedyncze wystąpienie filtru.

* Nie powinien być używany z filtrem, który zależy od usług z okresem istnienia innym niż pojedynczy.

Poniższy przykład pokazuje, jak przekazywać argumenty do typu przy użyciu `TypeFilterAttribute` :

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a>Filtry autoryzacji

Filtry autoryzacji:

* Są pierwszymi filtrami uruchamianymi w potoku filtru.
* Kontrola dostępu do metod akcji.
* Ma metodę Before, ale nie po metodzie.

Niestandardowe filtry autoryzacji wymagają niestandardowej struktury autoryzacji. Preferuj skonfigurowanie zasad autoryzacji lub zapisanie niestandardowych zasad autoryzacji przez zapisanie filtru niestandardowego. Wbudowany filtr autoryzacji:

* Wywołuje system autoryzacji.
* Nie zezwala na żądania.

**Nie** zgłaszaj wyjątków w ramach filtrów autoryzacji:

* Wyjątek nie zostanie obsłużony.
* Filtry wyjątków nie będą obsługiwać wyjątku.

Rozważ wygenerowanie wyzwania w przypadku wystąpienia wyjątku w filtrze autoryzacji.

Dowiedz się więcej o [autoryzacji](xref:security/authorization/introduction).

## <a name="resource-filters"></a>Filtry zasobów

Filtry zasobów:

* Zaimplementuj <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> interfejs lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> .
* Wykonanie powoduje Zawijanie większości potoku filtru.
* Tylko [filtry autoryzacji](#authorization-filters) są uruchamiane przed filtrami zasobów.

Filtry zasobów są przydatne do krótkiego obwodu większości potoku. Na przykład filtr buforowania może uniknąć pozostałej części potoku w pamięci podręcznej.

Przykłady filtru zasobów:

* [Filtr zasobów o krótkim obwodzie](#short-circuiting-resource-filter) pokazano wcześniej.
* [DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):

  * Uniemożliwia powiązanie modelu z dostępem do danych formularza.
  * Służy do przekazywania dużych plików w celu uniemożliwienia odczytywania danych formularza do pamięci.

## <a name="action-filters"></a>Filtry akcji

> [!IMPORTANT]
> Filtry akcji **nie** mają zastosowania do Razor stron. Razor Strony obsługują <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> i <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> . Aby uzyskać więcej informacji, zobacz [metody filtrowania dla Razor stron](xref:razor-pages/filter).

Filtry akcji:

* Zaimplementuj <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> interfejs lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> .
* Ich wykonanie otacza wykonywanie metod akcji.

Poniższy kod przedstawia przykładowy filtr akcji:

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext>Zawiera następujące właściwości:

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> -umożliwia odczytywanie danych wejściowych metody akcji.
* <xref:Microsoft.AspNetCore.Mvc.Controller> -Włącza manipulowanie wystąpieniem kontrolera.
* <xref:System.Web.Mvc.ActionExecutingContext.Result> -Ustawianie `Result` wykonywania krótkich obwodów metody akcji i kolejnych filtrów akcji.

Zgłaszanie wyjątku w metodzie akcji:

* Zapobiega uruchamianiu kolejnych filtrów.
* W przeciwieństwie do ustawienia `Result` , jest traktowany jako błąd, a nie pomyślny wynik.

<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext>Zawiera `Controller` i oraz `Result` oraz następujące właściwości:

* <xref:System.Web.Mvc.ActionExecutedContext.Canceled> -True, jeśli wykonywanie akcji zostało skrócone przez inny filtr.
* <xref:System.Web.Mvc.ActionExecutedContext.Exception> -O wartości innej niż null, jeśli filtr akcji lub poprzednio uruchomionego wywołał wyjątek. Ustawienie tej właściwości na wartość null:

  * Skutecznie obsługuje wyjątek.
  * `Result` jest wykonywany tak, jakby został zwrócony z metody akcji.

Dla `IAsyncActionFilter` , wywołanie <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> :

* Wykonuje wszystkie kolejne filtry akcji i metodę akcji.
* Zwraca wartość `ActionExecutedContext`.

Do krótkiego obwodu, przypisz <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> do wystąpienia wynikowego i nie wywołuj `next` ( `ActionExecutionDelegate` ).

Struktura zawiera abstrakcyjny <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , który może być podklasą.

`OnActionExecuting`Filtr akcji może służyć do:

* Zweryfikuj stan modelu.
* Zwróć błąd, jeśli stan jest nieprawidłowy.

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

`OnActionExecuted`Metoda jest uruchamiana po metodzie akcji:

* I mogą przeglądać wyniki akcji przez właściwość i manipulować nimi <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> .
* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> jest ustawiona na wartość true, jeśli wykonywanie akcji zostało skrócone przez inny filtr.
* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> ma ustawioną wartość różną od null, jeśli akcja lub kolejny filtr akcji wywołał wyjątek. Ustawienie `Exception` na wartość null:

  * Efektywnie obsługuje wyjątek.
  * `ActionExecutedContext.Result` jest wykonywany tak, jakby został zwrócony normalnie z metody akcji.

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a>Filtry wyjątków

Filtry wyjątków:

* Implementowanie <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter> . 
* Może służyć do implementowania typowych zasad obsługi błędów.

Następujący przykładowy filtr wyjątku używa niestandardowego widoku błędów, aby wyświetlić szczegóły dotyczące wyjątków występujących podczas opracowywania aplikacji:

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

Filtry wyjątków:

* Nie mam zdarzeń przed i po.
* Implementowanie <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*> .
* Obsługuj Nieobsłużone wyjątki występujące podczas Razor tworzenia strony lub kontrolera, [powiązania modelu](xref:mvc/models/model-binding), filtrów akcji lub metod akcji.
* **Nie** należy przechwytywać wyjątków występujących w filtrach zasobów, filtrach wyników ani wykonywaniu wyniku MVC.

Aby obsłużyć wyjątek, ustaw <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> Właściwość na `true` lub Zapisz odpowiedź. Spowoduje to zatrzymanie propagacji wyjątku. Filtr wyjątku nie może przekształcić wyjątku w "powodzenie". Można to zrobić tylko dla filtru akcji.

Filtry wyjątków:

* Są dobre dla wyjątków zalewkowania występujących w ramach akcji.
* Nie są tak elastyczne jak błędy obsługi oprogramowania pośredniczącego.

Preferuj oprogramowanie pośredniczące dla obsługi wyjątków. Użyj filtrów wyjątków tylko w przypadku, gdy obsługa błędów *różni* się w zależności od tego, która metoda akcji jest wywoływana. Na przykład aplikacja może mieć metody akcji zarówno dla punktów końcowych interfejsu API, jak i dla widoków/HTML. Punkty końcowe interfejsu API mogą zwracać informacje o błędach jako dane JSON, podczas gdy akcje oparte na widoku mogą zwrócić stronę błędu jako kod HTML.

## <a name="result-filters"></a>Filtry wyników

Filtry wyników:

* Zaimplementuj interfejs:
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> lub <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter>
* Ich wykonanie otacza wykonywanie wyników akcji.

### <a name="iresultfilter-and-iasyncresultfilter"></a>IResultFilter i IAsyncResultFilter

Poniższy kod pokazuje filtr wynikowy, który dodaje nagłówek HTTP:

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

Rodzaj wykonywanego wyniku zależy od akcji. Akcja zwracająca widok będzie obejmować wszystkie procesy przetwarzania Razor w ramach <xref:Microsoft.AspNetCore.Mvc.ViewResult> wykonywanych operacji. Metoda interfejsu API może wykonywać pewne serializacji w ramach wykonywania wyniku. Dowiedz się więcej o [wynikach akcji](xref:mvc/controllers/actions).

Filtry wynikowe są wykonywane tylko wtedy, gdy akcja lub filtr akcji generuje wynik akcji. Filtry wynikowe nie są wykonywane, gdy:

* Filtr autoryzacji lub filtr zasobów jest krótki obwody potoku.
* Filtr wyjątku obsługuje wyjątek przez wygenerowanie wyniku akcji.

<xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName>Metoda może skrócić wykonywanie wyniku akcji i kolejnych filtrów wynikowych przez ustawienie wartości <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> `true` . Zapisuj w obiekcie Response podczas krótkiego obwodu, aby uniknąć generowania pustej odpowiedzi. Zgłaszanie wyjątku w programie `IResultFilter.OnResultExecuting` spowoduje:

* Zapobiegaj wykonaniu wyniku akcji i kolejnych filtrów.
* Być traktowany jako niepowodzenie, a nie wynikowy pomyślnie.

Po <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> uruchomieniu metody odpowiedź została już wysłana do klienta. Jeśli odpowiedź została już wysłana do klienta, nie można jej zmienić.

`ResultExecutedContext.Canceled` jest ustawiona na, `true` Jeśli wykonywanie wyniku akcji zostało skrócone przez inny filtr.

`ResultExecutedContext.Exception` ma ustawioną wartość różną od null, jeśli wynik akcji lub kolejny filtr wyników wywołał wyjątek. Ustawienie `Exception` wartości null powoduje skuteczną obsługę wyjątku i uniemożliwia ponowne zgłoszenie wyjątku przez ASP.NET Core w dalszej części potoku. Nie istnieje niezawodny sposób zapisu danych do odpowiedzi podczas obsługi wyjątku w filtrze wynikowym. Jeśli nagłówki zostały opróżnione do klienta, gdy wynikiem akcji zgłasza wyjątek, nie istnieje niezawodny mechanizm wysyłania kodu błędu.

Dla <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> , wywołanie `await next` na <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> wykonuje wszystkie kolejne filtry wynikowe i wynik działania. Do krótkiego obwodu Ustaw [ResultExecutingContext. Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) na `true` i nie wywołuj `ResultExecutionDelegate` :

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

Struktura zawiera abstrakcyjny `ResultFilterAttribute` , który może być podklasą. Przedstawiona wcześniej Klasa [Addheaderattribute](#add-header-attribute) jest przykładem atrybutu filtru wynikowego.

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a>IAlwaysRunResultFilter i IAsyncAlwaysRunResultFilter

<xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter>Interfejsy i <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> deklarują <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementację, która jest uruchamiana dla wszystkich wyników akcji. Obejmuje to wyniki akcji generowane przez:

* Filtry autoryzacji i filtry zasobów, które są skracane.
* Filtry wyjątków.

Na przykład następujący filtr jest zawsze uruchamiany i ustawia wynik akcji ( <xref:Microsoft.AspNetCore.Mvc.ObjectResult> ) z kodem stanu *jednostki nieprzetwarzanych 422* , gdy negocjowanie zawartości kończy się niepowodzeniem:

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a>IFilterFactory

<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> . W związku z tym `IFilterFactory` wystąpienie może być używane jako `IFilterMetadata` wystąpienie w dowolnym miejscu w potoku filtru. Gdy środowisko uruchomieniowe przygotowuje się do wywołania filtru, próbuje rzutować go na `IFilterFactory` . Jeśli rzutowanie powiedzie się, <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> Metoda zostanie wywołana w celu utworzenia `IFilterMetadata` wystąpienia, które jest wywoływane. Zapewnia to elastyczny projekt, ponieważ dokładny potok filtru nie musi być ustawiony jawnie podczas uruchamiania aplikacji.

`IFilterFactory` można zaimplementować przy użyciu niestandardowych implementacji atrybutów jako inne podejście do tworzenia filtrów:

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

Poprzedni kod może być testowany przez uruchomienie [przykładu pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):

* Wywołaj narzędzia deweloperskie F12.
* Przejdź do adresu `https://localhost:5001/Sample/HeaderWithFactory`.

Narzędzia programistyczne F12 wyświetlają następujące nagłówki odpowiedzi dodane przez przykładowy kod:

* **autor:**`Joe Smith`
* **globaladdheader:**`Result filter added to MvcOptions.Filters`
* **wewnętrzny:**`My header`

Poprzedni kod tworzy nagłówek **Internal:** `My header` Response.

### <a name="ifilterfactory-implemented-on-an-attribute"></a>IFilterFactory zaimplementowane dla atrybutu

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

Filtry, które implementują `IFilterFactory` są przydatne dla filtrów, które:

* Nie wymagaj parametrów przekazujących.
* Mają zależności konstruktora, które muszą być wypełnione przez DI.

<xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> . `IFilterFactory` uwidacznia <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodę tworzenia <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> wystąpienia. `CreateInstance` Ładuje określony typ z kontenera usług (DI).

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

Poniższy kod przedstawia trzy podejścia do zastosowania `[SampleActionFilter]` :

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

W poprzednim kodzie dekorowania nazwy metodę with `[SampleActionFilter]` jest preferowanym podejściem do zastosowania `SampleActionFilter` .

## <a name="using-middleware-in-the-filter-pipeline"></a>Używanie oprogramowania pośredniczącego w potoku filtru

Filtry zasobów działają podobnie jak [oprogramowanie pośredniczące](xref:fundamentals/middleware/index) w celu obsłużenia wykonywania wszystkich elementów, które są późniejsze w potoku. Ale filtry różnią się od oprogramowania pośredniczącego w tym, że są częścią środowiska uruchomieniowego ASP.NET Core, co oznacza, że ma dostęp do ASP.NET Core kontekstu i konstrukcji.

Aby użyć oprogramowania pośredniczącego jako filtru, Utwórz typ z `Configure` metodą, która określa oprogramowanie pośredniczące, które ma zostać dodane do potoku filtru. W poniższym przykładzie jest wykorzystywane oprogramowanie pośredniczące lokalizacyjne do ustalenia bieżącej kultury dla żądania:

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

Użyj, <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> Aby uruchomić oprogramowanie pośredniczące:

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

Filtry oprogramowania pośredniczącego są uruchamiane na tym samym etapie potoku filtru jako filtry zasobów przed powiązaniem modelu i po pozostałej części potoku.

## <a name="next-actions"></a>Następne akcje

* Zobacz [metody filtrowania dla Razor stron](xref:razor-pages/filter).
* Aby eksperymentować z filtrami, należy [pobrać, przetestować i zmodyfikować przykład usługi GitHub](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).

::: moniker-end
