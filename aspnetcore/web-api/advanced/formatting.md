---
title: Formatowanie danych odpowiedzi w ASP.NET Core Web API
author: ardalis
description: Dowiedz się, jak sformatować dane odpowiedzi w ASP.NET Core Web API.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 04/17/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/advanced/formatting
ms.openlocfilehash: 8aa94bd1f33d1dd8ce8e7f50468ed60b4ccb2515
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019940"
---
# <a name="format-response-data-in-aspnet-core-web-api"></a>Formatowanie danych odpowiedzi w ASP.NET Core Web API

Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT) i [Steve Smith](https://ardalis.com/)

ASP.NET Core MVC obsługuje formatowanie danych odpowiedzi. Dane odpowiedzi można sformatować przy użyciu określonych formatów lub w odpowiedzi na żądany format klienta.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="format-specific-action-results"></a>Wyniki akcji specyficzne dla formatu

Niektóre typy wyników akcji są specyficzne dla określonego formatu, takiego jak <xref:Microsoft.AspNetCore.Mvc.JsonResult> i <xref:Microsoft.AspNetCore.Mvc.ContentResult> . Akcje mogą zwracać wyniki sformatowane w określonym formacie, niezależnie od preferencji klienta. Na przykład zwracanie `JsonResult` zwraca dane w formacie JSON. Zwracanie `ContentResult` lub ciąg zwraca dane ciągu w formacie zwykłego tekstu.

Akcja nie jest wymagana do zwrócenia żadnego określonego typu. ASP.NET Core obsługuje dowolną wartość zwracaną przez obiekt.  Wyniki akcji, które zwracają obiekty, które nie są <xref:Microsoft.AspNetCore.Mvc.IActionResult> typami, są serializowane przy użyciu odpowiedniej <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementacji. Aby uzyskać więcej informacji, zobacz <xref:web-api/action-return-types>.

Wbudowana metoda pomocnika <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> zwraca dane sformatowane w formacie JSON:[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]

Pobieranie próbek zwraca listę autorów. Za pomocą narzędzi deweloperskich przeglądarki F12 lub [po](https://www.getpostman.com/tools) powyższym kodzie:

* Zostanie wyświetlony nagłówek odpowiedzi zawierający **Typ zawartości:** `application/json; charset=utf-8` .
* Wyświetlane są nagłówki żądań. Na przykład `Accept` nagłówek. `Accept`Nagłówek jest ignorowany przez poprzedni kod.

Aby zwrócić dane w formacie zwykłego tekstu, użyj <xref:Microsoft.AspNetCore.Mvc.ContentResult> i <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Content%2A> pomocnika:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

W powyższym kodzie `Content-Type` zwraca wartość `text/plain` . Zwracanie ciągu `Content-Type` z `text/plain` :

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

W przypadku akcji z wieloma zwracanymi typami zwracamy `IActionResult` . Na przykład zwrócenie różnych kodów stanu HTTP w oparciu o wynik wykonanych operacji.

## <a name="content-negotiation"></a>Negocjowanie zawartości

Negocjowanie zawartości odbywa się, gdy klient określi [nagłówek Accept](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html). Domyślny format używany przez ASP.NET Core to [JSON](https://json.org/). Negocjowanie zawartości:

* Zaimplementowane przez <xref:Microsoft.AspNetCore.Mvc.ObjectResult> .
* Wbudowane wyniki akcji specyficzne dla kodu stanu zwracane z metod pomocnika. Metody pomocnika wyników akcji są oparte na `ObjectResult` .

Po zwróceniu typu modelu zwracanym typem jest `ObjectResult` .

W poniższej metodzie działania są `Ok` stosowane `NotFound` metody pomocnika i:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

Domyślnie ASP.NET Core obsługuje `application/json` `text/json` `text/plain` typy nośników, i. Narzędzia takie jak [programu Fiddler](https://www.telerik.com/fiddler) lub [Poster](https://www.getpostman.com/tools) mogą ustawić `Accept` nagłówek żądania w celu określenia formatu zwrotnego. Gdy `Accept` Nagłówek zawiera typ obsługiwany przez serwer, zwracany jest ten typ. W następnej sekcji pokazano, jak dodać dodatkowe elementy formatujące.

Akcje kontrolera mogą zwracać POCOs (zwykłe stare obiekty CLR). Gdy zostanie zwrócona wartość POCO, środowisko uruchomieniowe automatycznie tworzy `ObjectResult` , które zawija obiekt. Klient pobiera sformatowany obiekt Zserializowany. Jeśli zwracany obiekt jest `null` `204 No Content` zwracany, zwracana jest odpowiedź.

Zwracanie typu obiektu:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

W poprzednim kodzie żądanie poprawnego aliasu autora zwraca `200 OK` odpowiedź z danymi autora. Żądanie dotyczące nieprawidłowego aliasu zwraca `204 No Content` odpowiedź.

### <a name="the-accept-header"></a>Nagłówek Accept

*Negocjowanie* zawartości odbywa się po `Accept` pojawieniu się nagłówka w żądaniu. Gdy żądanie zawiera nagłówek Accept, ASP.NET Core:

* Wylicza typy nośników w nagłówku Accept w kolejności preferencji.
* Próbuje znaleźć program formatujący, który może wygenerować odpowiedź w jednym z określonych formatów.

Jeśli nie zostanie znaleziony żaden program formatujący, który może spełnić żądanie klienta, ASP.NET Core:

* Zwraca `406 Not Acceptable` wartość <xref:Microsoft.AspNetCore.Mvc.MvcOptions> , jeśli została ustawiona, lub-
* Próbuje znaleźć pierwszy program formatujący, który może wygenerować odpowiedź.

Jeśli nie skonfigurowano programu formatującego dla żądanego formatu, jest używany pierwszy program formatujący, który może formatować obiekt. Jeśli `Accept` w żądaniu nie zostanie wyświetlony nagłówek:

* Pierwszy program formatujący, który może obsłużyć obiekt, jest używany do serializacji odpowiedzi.
* Nie ma żadnej negocjacji. Serwer określa format do zwrócenia.

Jeśli nagłówek Accept zawiera `*/*` , nagłówek jest ignorowany, chyba że `RespectBrowserAcceptHeader` jest ustawiona na wartość true (prawda) <xref:Microsoft.AspNetCore.Mvc.MvcOptions> .

### <a name="browsers-and-content-negotiation"></a>Przeglądarki i negocjacje zawartości

W przeciwieństwie do typowych klientów interfejsu API, przeglądarki sieci Web dostarczają `Accept` nagłówki. Przeglądarka sieci Web określa wiele formatów, w tym symboli wieloznacznych. Domyślnie, gdy struktura wykryje, że żądanie pochodzi z przeglądarki:

* `Accept`Nagłówek jest ignorowany.
* Zawartość jest zwracana w formacie JSON, o ile nie została skonfigurowana inaczej.

Zapewnia to bardziej spójne środowisko w przeglądarkach podczas używania interfejsów API.

Aby skonfigurować aplikację do honorowania nagłówków akceptowanych przez przeglądarkę, ustaw wartość <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> na `true` :

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a>Konfigurowanie elementów formatujących

Aplikacje, które muszą obsługiwać dodatkowe formaty, mogą dodać odpowiednie pakiety NuGet i skonfigurować obsługę. Istnieją osobne elementy formatujące dla danych wejściowych i wyjściowych. Wejściowe elementy formatującego są używane przez [powiązanie modelu](xref:mvc/models/model-binding). Wyjściowe elementy formatujące są używane do formatowania odpowiedzi. Aby uzyskać informacje na temat tworzenia niestandardowego programu formatującego, zobacz [niestandardowe elementy formatujące](xref:web-api/advanced/custom-formatters).

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a>Dodawanie obsługi formatu XML

Elementy formatujące XML zaimplementowane przy użyciu <xref:System.Xml.Serialization.XmlSerializer> są konfigurowane przez wywołanie <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> :

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

Poprzedni kod serializacji wyników przy użyciu `XmlSerializer` .

W przypadku korzystania z powyższego kodu metody kontrolera zwracają odpowiedni format na podstawie nagłówka żądania `Accept` .

### <a name="configure-systemtextjson-based-formatters"></a>Konfigurowanie System.Text.Jsopartych na programie formatującegos

Funkcje dla elementów `System.Text.Json` formatujących opartych na programie można skonfigurować przy użyciu polecenia `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions` .

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.JsonSerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.JsonSerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

Opcje serializacji danych wyjściowych dla poszczególnych akcji można skonfigurować przy użyciu polecenia `JsonResult` . Przykład:

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a>Dodawanie obsługi formatu JSON Newtonsoft.Jsna podstawie

Przed ASP.NET Core 3,0 stosowane są domyślne elementy formatujące JSON zaimplementowane przy użyciu `Newtonsoft.Json` pakietu. W ASP.NET Core 3,0 lub nowszych domyślne elementy formatujące JSON są oparte na `System.Text.Json` . Obsługa `Newtonsoft.Json` opartych na programie formatującego i funkcji jest dostępna przez zainstalowanie [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) pakietu NuGet i skonfigurowanie go w programie `Startup.ConfigureServices` .

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

Niektóre funkcje mogą nie współdziałać z modułami `System.Text.Json` formatującego opartymi na języku i wymagają odwołania do elementów `Newtonsoft.Json` formatujących opartych na bazie. Kontynuuj korzystanie z programu `Newtonsoft.Json` formatującego w oparciu o aplikacje:

* Używa `Newtonsoft.Json` atrybutów. Na przykład: `[JsonProperty]` lub `[JsonIgnore]`.
* Dostosowuje ustawienia serializacji.
* Opiera się na `Newtonsoft.Json` udostępnianych funkcjach.
* Konfiguruje `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings` . Przed ASP.NET Core 3,0, `JsonResult.SerializerSettings` akceptuje wystąpienie `JsonSerializerSettings` , które jest specyficzne dla `Newtonsoft.Json` .
* Generuje dokumentację [openapi](<xref:tutorials/web-api-help-pages-using-swagger>) .

Funkcje dla elementów `Newtonsoft.Json` formatujących opartych na programie można skonfigurować przy użyciu `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings` :

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerSettings.Converters.Add(new MyCustomJsonConverter());
});
```

Opcje serializacji danych wyjściowych dla poszczególnych akcji można skonfigurować przy użyciu polecenia `JsonResult` . Przykład:

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerSettings
    {
        Formatting = Formatting.Indented,
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

### <a name="add-xml-format-support"></a>Dodawanie obsługi formatu XML

Formatowanie XML wymaga pakietu NuGet [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) .

Elementy formatujące XML zaimplementowane przy użyciu <xref:System.Xml.Serialization.XmlSerializer> są konfigurowane przez wywołanie <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> :

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

Poprzedni kod serializacji wyników przy użyciu `XmlSerializer` .

W przypadku korzystania z powyższego kodu metody kontrolera powinny zwrócić odpowiedni format na podstawie nagłówka żądania `Accept` .

::: moniker-end

### <a name="specify-a-format"></a>Określ format

Aby ograniczyć formaty odpowiedzi, Zastosuj [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) Filtr. Podobnie jak większość [filtrów](xref:mvc/controllers/filters), `[Produces]` można zastosować do akcji, kontrolera lub zakresu globalnego:

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

Poprzedni [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) Filtr:

* Wymusza, aby wszystkie akcje w ramach kontrolera zwracały odpowiedzi w formacie JSON.
* Jeśli inne elementy formatujące są skonfigurowane, a klient określi inny format, zwracany jest kod JSON.

Aby uzyskać więcej informacji, zobacz [filtry](xref:mvc/controllers/filters).

### <a name="special-case-formatters"></a>Specjalne elementy formatujące Case

Niektóre specjalne przypadki są implementowane przy użyciu wbudowanych elementów formatujących. Domyślnie `string` typy zwracane są formatowane jako *tekst/zwykły* (*text/html* , jeśli żąda się za pośrednictwem `Accept` nagłówka). Takie zachowanie można usunąć, usuwając <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter> . Elementy formatujące są usuwane w `ConfigureServices` metodzie. Akcje, które mają typ zwracany obiektu modelu zwracają, `204 No Content` gdy zwracają `null` . Takie zachowanie można usunąć, usuwając <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter> . Poniższy kod usuwa `StringOutputFormatter` i `HttpNoContentOutputFormatter` .

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

Bez `StringOutputFormatter` , wbudowany typ programu FORMATUJĄCEGO JSON formatuje `string` typy zwracane. Jeśli wbudowany program formatujący JSON jest usuwany, a element formatujący XML jest dostępny, format XML programu formatującego jest `string` typem zwracanym. W przeciwnym razie zwracane `string` typy zwracają `406 Not Acceptable` .

Bez `HttpNoContentOutputFormatter` obiektów o wartości null są formatowane przy użyciu skonfigurowanego programu formatującego. Przykład:

* Program formatujący JSON zwraca odpowiedź z treścią `null` .
* Program formatujący XML zwraca pusty element XML z `xsi:nil="true"` zestawem atrybutów.

## <a name="response-format-url-mappings"></a>Mapowania adresów URL w formacie odpowiedzi

Klienci mogą zażądać określonego formatu w ramach adresu URL, na przykład:

* W ciągu zapytania lub części ścieżki.
* Przy użyciu rozszerzenia pliku specyficznego dla formatu, takiego jak. XML lub. JSON.

Mapowanie ze ścieżki żądania należy określić w marszrucie używanej przez interfejs API. Przykład:

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

Poprzednia trasa pozwala określić żądany format jako opcjonalne rozszerzenie pliku. [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute)Atrybut sprawdza obecność wartości format w `RouteData` i mapuje format odpowiedzi do odpowiedniego programu formatującego podczas tworzenia odpowiedzi.

|           Trasa        |             EQ              |
|------------------------|------------------------------------|
|   `/api/products/5`    |    Domyślny program formatujący dane wyjściowe    |
| `/api/products/5.json` | Program formatujący JSON (jeśli jest skonfigurowany) |
| `/api/products/5.xml`  | Program formatujący XML (jeśli jest skonfigurowany)  |
