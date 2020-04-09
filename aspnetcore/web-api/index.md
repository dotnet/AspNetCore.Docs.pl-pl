---
title: Tworzenie interfejsów API sieci Web za pomocą ASP.NET Core
author: scottaddie
description: Poznaj podstawy tworzenia internetowego interfejsu API w ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/02/2020
uid: web-api/index
ms.openlocfilehash: be88b8d58f1f660f3a815c395c210c05a7b4917c
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666006"
---
# <a name="create-web-apis-with-aspnet-core"></a>Tworzenie interfejsów API sieci Web za pomocą ASP.NET Core

Przez [Scott Addie](https://github.com/scottaddie) i [Tom Dykstra](https://github.com/tdykstra)

Platforma ASP.NET Core obsługuje tworzenie usług RESTful, znanych także jako internetowe interfejsy API, za pomocą języka C#. Do obsługi żądań internetowy interfejs API używa kontrolerów. *Kontrolery* w internetowym interfejsie API `ControllerBase`to klasy, które wynikają z programu . W tym artykule pokazano, jak używać kontrolerów do obsługi żądań interfejsu API sieci Web.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples). (Jak[pobrać](xref:index#how-to-download-a-sample)).

## <a name="controllerbase-class"></a>Klasa ControllerBase

Internetowy interfejs API składa się z jednej <xref:Microsoft.AspNetCore.Mvc.ControllerBase>lub więcej klas kontrolerów, które wynikają z programu . Szablon projektu interfejsu API sieci web zapewnia kontroler startowy:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

Nie należy tworzyć kontrolera interfejsu API <xref:Microsoft.AspNetCore.Mvc.Controller> sieci web przez wyprowadzanie z klasy. `Controller`pochodzi z `ControllerBase` i dodaje obsługę widoków, więc jest do obsługi stron sieci web, a nie żądań interfejsu API sieci web. Istnieje wyjątek od tej reguły: jeśli planujesz używać tego samego kontrolera zarówno dla `Controller`widoków, jak i interfejsów API sieci Web, wydziel go z pliku .

Klasa `ControllerBase` zawiera wiele właściwości i metod, które są przydatne do obsługi żądań HTTP. Na przykład `ControllerBase.CreatedAtAction` zwraca kod stanu 201:

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=10)]

Oto kilka przykładów metod, `ControllerBase` które zapewnia.

|Metoda   |Uwagi    |
|---------|---------|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest%2A>| Zwraca kod stanu 400.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>|Zwraca kod stanu 404.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.PhysicalFile%2A>|Zwraca plik.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync%2A>|Wywołuje [powiązanie modelu](xref:mvc/models/model-binding).|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryValidateModel%2A>|Wywołuje [sprawdzanie poprawności modelu](xref:mvc/models/validation).|

Aby uzyskać listę wszystkich dostępnych metod <xref:Microsoft.AspNetCore.Mvc.ControllerBase>i właściwości, zobacz .

## <a name="attributes"></a>Atrybuty

Obszar <xref:Microsoft.AspNetCore.Mvc> nazw zawiera atrybuty, które mogą służyć do konfigurowania zachowania kontrolerów interfejsu API sieci web i metod akcji. W poniższym przykładzie użyto atrybutów do określenia obsługiwanego zlecenia akcji HTTP i wszelkich znanych kodów stanu HTTP, które mogą zostać zwrócone:

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=1-3)]

Oto kilka przykładów atrybutów, które są dostępne.

|Atrybut|Uwagi|
|---------|-----|
|[`[Route]`](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)      |Określa wzorzec adresu URL dla kontrolera lub akcji.|
|[`[Bind]`](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)        |Określa prefiks i właściwości do uwzględnienia dla powiązania modelu.|
|[`[HttpGet]`](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)  |Identyfikuje akcję, która obsługuje zlecenie akcji HTTP GET.|
|[`[Consumes]`](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)|Określa typy danych akceptowane przez akcję.|
|[`[Produces]`](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)|Określa typy danych zwracanych przez akcję.|

Aby uzyskać listę zawierającą dostępne <xref:Microsoft.AspNetCore.Mvc> atrybuty, zobacz obszar nazw.

## <a name="apicontroller-attribute"></a>Atrybut ApiController

Atrybut [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) można zastosować do klasy kontrolera, aby włączyć następujące opinionated, zachowania specyficzne dla interfejsu API:

::: moniker range=">= aspnetcore-2.2"

* [Wymaganie routingu atrybutów](#attribute-routing-requirement)
* [Automatyczne odpowiedzi HTTP 400](#automatic-http-400-responses)
* [Wnioskowanie o parametr źródła wiązania](#binding-source-parameter-inference)
* [Wnioskowanie z żądaniami wieloczęściowymi/formularzowymi danymi](#multipartform-data-request-inference)
* [Szczegóły problemu dla kodów stanu błędów](#problem-details-for-error-status-codes)

Funkcja *Kody stanu błędu* wymaga wersji zgodności 2.2 lub nowszej. [compatibility version](xref:mvc/compatibility-version) Inne funkcje wymagają wersji zgodności 2.1 lub nowszej.

::: moniker-end

::: moniker range="= aspnetcore-2.1"

* [Wymaganie routingu atrybutów](#attribute-routing-requirement)
* [Automatyczne odpowiedzi HTTP 400](#automatic-http-400-responses)
* [Wnioskowanie o parametr źródła wiązania](#binding-source-parameter-inference)
* [Wnioskowanie z żądaniami wieloczęściowymi/formularzowymi danymi](#multipartform-data-request-inference)

Te funkcje wymagają [wersji zgodności](xref:mvc/compatibility-version) 2.1 lub nowszej.

::: moniker-end

### <a name="attribute-on-specific-controllers"></a>Atrybut na określonych kontrolerach

Atrybut `[ApiController]` można zastosować do określonych kontrolerów, jak w poniższym przykładzie z szablonu projektu:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2])]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=2)]

::: moniker-end

### <a name="attribute-on-multiple-controllers"></a>Atrybut na wielu kontrolerach

Jednym z podejść do używania atrybutu na więcej niż jednym kontrolerze `[ApiController]` jest utworzenie niestandardowej klasy kontrolera podstawowego z adnotacją o tym atrybutem. W poniższym przykładzie przedstawiono niestandardową klasę podstawową i kontroler, który pochodzi od niej:

[!code-csharp[](index/samples/2.x/2.2/Controllers/MyControllerBase.cs?name=snippet_MyControllerBase)]

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="attribute-on-an-assembly"></a>Atrybut w zestawie

Jeśli wersja zgodności jest [ustawiona](xref:mvc/compatibility-version) na 2.2 lub nowszą, `[ApiController]` atrybut można zastosować do zestawu. Adnotacja w ten sposób stosuje zachowanie interfejsu API sieci web do wszystkich kontrolerów w zestawie. Nie ma możliwości rezygnacji poszczególnych administratorów. Zastosuj atrybut na poziomie zestawu do deklaracji `Startup` obszaru nazw otaczającej klasę:

```csharp
[assembly: ApiController]
namespace WebApiSample
{
    public class Startup
    {
        ...
    }
}
```

::: moniker-end

## <a name="attribute-routing-requirement"></a>Wymaganie routingu atrybutów

Atrybut `[ApiController]` sprawia, że routing atrybutu wymaganie. Przykład:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2)]

Działania są niedostępne za pośrednictwem [konwencjonalnych tras](xref:mvc/controllers/routing#conventional-routing) <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> określonych `Startup.Configure`przez `UseEndpoints`, <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>lub w .

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=1)]

Działania są niedostępne za pośrednictwem [konwencjonalnych](xref:mvc/controllers/routing#conventional-routing) <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> tras `Startup.Configure`określonych przez <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> lub w .

::: moniker-end

## <a name="automatic-http-400-responses"></a>Automatyczne odpowiedzi HTTP 400

Atrybut `[ApiController]` sprawia, że błędy sprawdzania poprawności modelu automatycznie wyzwalają odpowiedź HTTP 400. W związku z tym następujący kod jest zbędne w metodzie akcji:

```csharp
if (!ModelState.IsValid)
{
    return BadRequest(ModelState);
}
```

ASP.NET Core MVC używa filtru <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter> akcji do wykonania poprzedniego sprawdzenia.

### <a name="default-badrequest-response"></a>Domyślna odpowiedź BadRequest

W wersji zgodności 2.1 domyślnym typem odpowiedzi dla odpowiedzi HTTP <xref:Microsoft.AspNetCore.Mvc.SerializableError>400 jest . Następująca treść żądania jest przykładem typu serializowanego:

```json
{
  "": [
    "A non-empty request body is required."
  ]
}
```

::: moniker range=">= aspnetcore-2.2"

W przypadku wersji zgodności 2.2 lub nowszej domyślnym typem odpowiedzi <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>dla odpowiedzi HTTP 400 jest . Następująca treść żądania jest przykładem typu serializowanego:

```json
{
  "type": "https://tools.ietf.org/html/rfc7231#section-6.5.1",
  "title": "One or more validation errors occurred.",
  "status": 400,
  "traceId": "|7fb5e16a-4c8f23bbfc974667.",
  "errors": {
    "": [
      "A non-empty request body is required."
    ]
  }
}
```

Typ: `ValidationProblemDetails`

* Zapewnia format do odczytu maszynowego do określania błędów w odpowiedziach interfejsu API sieci Web.
* Zgodność ze [specyfikacją RFC 7807](https://tools.ietf.org/html/rfc7807).

::: moniker-end

### <a name="log-automatic-400-responses"></a>Rejestrowanie automatycznych odpowiedzi 400

Zobacz [Jak rejestrować automatyczne 400 odpowiedzi na błędy sprawdzania poprawności modelu (aspnet/AspNetCore.Docs #12157)](https://github.com/dotnet/AspNetCore.Docs/issues/12157).

### <a name="disable-automatic-400-response"></a>Wyłącz automatyczną odpowiedź 400

Aby wyłączyć automatyczne zachowanie 400, `true`ustaw właściwość na <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> . Dodaj następujący wyróżniony `Startup.ConfigureServices`kod w :

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,6)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,5)]

::: moniker-end

## <a name="binding-source-parameter-inference"></a>Wnioskowanie o parametr źródła wiązania

Atrybut źródła powiązania definiuje lokalizację, w której znajduje się wartość parametru akcji. Istnieją następujące atrybuty źródła powiązania:

|Atrybut|Źródło wiązania |
|---------|---------|
|[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)     | Treść żądania |
|[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)     | Dane formularza w treści żądania |
|[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) | Nagłówek żądania |
|[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)   | Zażądaj parametru ciągu kwerendy |
|[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)   | Rozsyłanie danych z bieżącego żądania |
|[`[FromServices]`](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices) | Usługa żądania wstrzyknięta jako parametr akcji |

> [!WARNING]
> Nie używaj, `[FromRoute]` gdy `%2f` wartości mogą `/`zawierać (czyli). `%2f`nie będzie unescaped `/`do . Użyj, `[FromQuery]` jeśli wartość `%2f`może zawierać .

Bez `[ApiController]` atrybutów źródła atrybutów `[FromQuery]`atrybutów, takich jak , ASP.NET Core runtime próbuje użyć spinacza modelu obiektów złożonych. Spinacz złożonego modelu obiektu pobiera dane od dostawców wartości w zdefiniowanej kolejności.

W poniższym przykładzie `[FromQuery]` atrybut wskazuje, `discontinuedOnly` że wartość parametru znajduje się w ciągu zapytania adresu URL żądania:

[!code-csharp[](index/samples/2.x/2.2/Controllers/ProductsController.cs?name=snippet_BindingSourceAttributes&highlight=3)]

Atrybut `[ApiController]` stosuje reguły wnioskowania dla domyślnych źródeł danych parametrów akcji. Te reguły pozwalają zaoszczędzić od konieczności ręcznego identyfikowania źródeł wiązania przez zastosowanie atrybutów do parametrów akcji. Reguły wnioskowania źródła wiązania zachowują się w następujący sposób:

* `[FromBody]`jest wywnioskowany dla złożonych parametrów typu. Wyjątkiem od `[FromBody]` reguły wnioskowania jest dowolny złożony, wbudowany typ <xref:Microsoft.AspNetCore.Http.IFormCollection> o <xref:System.Threading.CancellationToken>specjalnym znaczeniu, takim jak i . Kod wnioskowania źródła powiązania ignoruje te typy specjalne.
* `[FromForm]`wywnioskować parametry <xref:Microsoft.AspNetCore.Http.IFormFile> akcji <xref:Microsoft.AspNetCore.Http.IFormFileCollection>typu i . Nie jest wywnioskowane dla żadnych typów prostych lub zdefiniowanych przez użytkownika.
* `[FromRoute]`jest wywnioskowany dla dowolnej nazwy parametru akcji pasującej do parametru w szablonie trasy. Gdy więcej niż jedna trasa pasuje do parametru akcji, uwzględniana `[FromRoute]`jest dowolna wartość trasy.
* `[FromQuery]`wywnioskować dla innych parametrów akcji.

### <a name="frombody-inference-notes"></a>Notatki wnioskowania FromBody

`[FromBody]`nie jest wywnioskowany `string` dla `int`prostych typów, takich jak lub . W związku `[FromBody]` z tym atrybut powinien być używany dla typów prostych, gdy ta funkcja jest potrzebna.

Gdy akcja ma więcej niż jeden parametr związany z treści żądania, wyjątek. Na przykład wszystkie następujące podpisy metody akcji powodują wyjątek:

* `[FromBody]`wywnioskować na obu, ponieważ są one złożone typy.

  ```csharp
  [HttpPost]
  public IActionResult Action1(Product product, Order order)
  ```

* `[FromBody]`atrybut na jednym, wywnioskować na drugim, ponieważ jest to typ złożony.

  ```csharp
  [HttpPost]
  public IActionResult Action2(Product product, [FromBody] Order order)
  ```

* `[FromBody]`atrybutu na obu.

  ```csharp
  [HttpPost]
  public IActionResult Action3([FromBody] Product product, [FromBody] Order order)
  ```

::: moniker range="= aspnetcore-2.1"

> [!NOTE]
> W ASP.NET Core 2.1 parametry typu kolekcji, takie jak listy i `[FromQuery]`tablice, są niepoprawnie wywnioskowane jako . Atrybut `[FromBody]` powinien być używany dla tych parametrów, jeśli mają być powiązane z treści żądania. To zachowanie jest korygowane w ASP.NET Core 2.2 lub nowszym, gdzie parametry typu kolekcji są domyślnie powiązane z treścią.

::: moniker-end

### <a name="disable-inference-rules"></a>Wyłączanie reguł wnioskowania

Aby wyłączyć wnioskowanie o <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> `true`źródle wiązania, ustaw na . Dodaj następujący kod `Startup.ConfigureServices`w :

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,5)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,6)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,4)]

::: moniker-end

## <a name="multipartform-data-request-inference"></a>Wnioskowanie z żądaniami wieloczęściowymi/formularzowymi danymi

Atrybut `[ApiController]` stosuje regułę wnioskowania, gdy parametr akcji jest [`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) opisywany z atrybutem. Typ `multipart/form-data` zawartości żądania jest wywnioskowany.

Aby wyłączyć zachowanie domyślne, `true` ustaw `Startup.ConfigureServices`właściwość na: <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,4)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,5)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,3)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="problem-details-for-error-status-codes"></a>Szczegóły problemu dla kodów stanu błędów

Gdy wersja zgodności wynosi 2.2 lub nowsze, MVC przekształca wynik błędu (wynik o kodzie stanu <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>400 lub wyższym) na wynik z . Typ `ProblemDetails` jest oparty na [specyfikacji RFC 7807](https://tools.ietf.org/html/rfc7807) do dostarczania szczegółów błędu do odczytu maszynowego w odpowiedzi HTTP.

Należy wziąć pod uwagę następujący kod w akcji kontrolera:

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_ProblemDetailsStatusCode)]

Metoda `NotFound` tworzy kod stanu HTTP 404 `ProblemDetails` z treścią. Przykład:

```json
{
  type: "https://tools.ietf.org/html/rfc7231#section-6.5.4",
  title: "Not Found",
  status: 404,
  traceId: "0HLHLV31KRN83:00000001"
}
```

### <a name="disable-problemdetails-response"></a>Wyłącz odpowiedź ProblemDetails

Automatyczne tworzenie kodów stanu błędu `ProblemDetails` dla <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors%2A> jest wyłączone, `true`gdy właściwość jest ustawiona na . Dodaj następujący kod `Startup.ConfigureServices`w :

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,8)]

::: moniker-end

<a name="consumes"></a>

## <a name="define-supported-request-content-types-with-the-consumes-attribute"></a>Definiowanie obsługiwanych typów zawartości żądań za pomocą atrybutu [Zużywa]

Domyślnie akcja obsługuje wszystkie dostępne typy zawartości żądania. Jeśli na przykład aplikacja jest skonfigurowana do obsługi [formaterów wejściowych](xref:mvc/models/model-binding#input-formatters)JSON i XML, akcja obsługuje wiele typów zawartości, w tym `application/json` i `application/xml`.

[Atrybut [Zużywa]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) umożliwia akcję, aby ograniczyć typy zawartości obsługiwanych żądań. Zastosuj `[Consumes]` atrybut do akcji lub kontrolera, określając jeden lub więcej typów zawartości:

```csharp
[HttpPost]
[Consumes("application/xml")]
public IActionResult CreateProduct(Product product)
```

W poprzednim kodzie `CreateProduct` akcja określa typ `application/xml`zawartości . Żądania kierowane do tej `Content-Type` akcji `application/xml`muszą określać nagłówek . Żądania, które nie `Content-Type` określają `application/xml` nagłówka wyniku w [415 nieobsługiwał](https://developer.mozilla.org/docs/Web/HTTP/Status/415) typ nośnika odpowiedzi.

Atrybut `[Consumes]` umożliwia również akcję, aby wpłynąć na jego wybór na podstawie typu zawartości żądania przychodzącego przez zastosowanie ograniczenia typu. Rozważmy następujący przykład:

[!code-csharp[](index/samples/3.x/Controllers/ConsumesController.cs?name=snippet_Class)]

W poprzednim kodzie `ConsumesController` jest skonfigurowany do obsługi `https://localhost:5001/api/Consumes` żądań wysyłanych do adresu URL. Obie akcje kontrolera i `PostJson` `PostForm`, obsługiwać żądania POST z tym samym adresem URL. Bez `[Consumes]` atrybutu stosowania ograniczenia typu, niejednoznaczny wyjątek dopasowania jest zgłaszany.

Atrybut `[Consumes]` jest stosowany do obu akcji. Akcja `PostJson` obsługuje żądania wysyłane `Content-Type` z `application/json`nagłówkiem . Akcja `PostForm` obsługuje żądania wysyłane `Content-Type` z `application/x-www-form-urlencoded`nagłówkiem . 

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:web-api/action-return-types>
* <xref:web-api/handle-errors>
* <xref:web-api/advanced/custom-formatters>
* <xref:web-api/advanced/formatting>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:mvc/controllers/routing>
