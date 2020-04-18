---
title: Formatowanie danych odpowiedzi w ASP.NET Core Web API
author: ardalis
description: Dowiedz się, jak formatować dane odpowiedzi w ASP.NET Core Web API.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 04/17/2020
uid: web-api/advanced/formatting
ms.openlocfilehash: 392e4905126ffb6801cc55055f1d511f5fa99dd1
ms.sourcegitcommit: 3d07e21868dafc503530ecae2cfa18a7490b58a6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81642709"
---
# <a name="format-response-data-in-aspnet-core-web-api"></a>Formatowanie danych odpowiedzi w ASP.NET Core Web API

Przez [Rick Anderson](https://twitter.com/RickAndMSFT) i Steve [Smith](https://ardalis.com/)

ASP.NET Core MVC obsługuje formatowanie danych odpowiedzi. Dane odpowiedzi mogą być formatowane przy użyciu określonych formatów lub w odpowiedzi na żądany format klienta.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="format-specific-action-results"></a>Wyniki akcji specyficzne dla formatu

Niektóre typy wyników akcji są specyficzne <xref:Microsoft.AspNetCore.Mvc.JsonResult> dla <xref:Microsoft.AspNetCore.Mvc.ContentResult>określonego formatu, na przykład i . Akcje mogą zwracać wyniki sformatowane w określonym formacie, niezależnie od preferencji klienta. Na przykład zwraca `JsonResult` dane w formacie JSON. Zwracanie `ContentResult` lub ciąg zwraca dane ciągu w formacie zwykłego tekstu.

Akcja nie jest wymagana do zwrócenia dowolnego określonego typu. ASP.NET Core obsługuje dowolną wartość zwracaną obiektu.  Wyniki z akcji, które <xref:Microsoft.AspNetCore.Mvc.IActionResult> zwracają obiekty, które <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> nie są typami są serializowane przy użyciu odpowiedniej implementacji. Aby uzyskać więcej informacji, zobacz <xref:web-api/action-return-types>.

Wbudowana metoda <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> pomocnika zwraca dane w formacie JSON:[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]

Przykładowe pobieranie zwraca listę autorów. Korzystanie z narzędzi programistycznych przeglądarki F12 lub [Listonosz](https://www.getpostman.com/tools) z poprzednim kodem:

* Zostanie wyświetlony nagłówek odpowiedzi zawierający **typ zawartości.** `application/json; charset=utf-8`
* Zostaną wyświetlone nagłówki żądań. Na przykład `Accept` nagłówek. Nagłówek `Accept` jest ignorowany przez poprzedni kod.

Aby zwrócić dane sformatowane w postaci zwykłego tekstu, użyj <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> i <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> pomocnika:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

W poprzednim kodzie `Content-Type` zwracany `text/plain`jest . Zwracanie ciągu `Content-Type` `text/plain`zapewnia:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

W przypadku akcji z `IActionResult`wieloma typami zwracania należy zwrócić . Na przykład zwracanie różnych kodów stanu HTTP na podstawie wyniku wykonanych operacji.

## <a name="content-negotiation"></a>Negocjacje treści

Negocjacja zawartości odbywa się, gdy klient określa [nagłówek Akceptuj](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html). Domyślnym formatem używanym przez ASP.NET Core jest [JSON](https://json.org/). Negocjacje treści to:

* Zaimplementowane przez <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.
* Wbudowane w wyniki akcji specyficzne dla kodu stanu zwrócone z metod pomocnika. Metody pomocnicze wyników akcji `ObjectResult`są oparte na programie .

Po zwróceniu typu modelu, `ObjectResult`typem zwracanym jest .

Następująca metoda akcji `Ok` używa `NotFound` i metody pomocnicze:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

Domyślnie ASP.NET core obsługuje `application/json`i `text/json`typy `text/plain` nośników. Narzędzia, takie jak [Fiddler](https://www.telerik.com/fiddler) lub `Accept` [Listonosz,](https://www.getpostman.com/tools) mogą ustawić nagłówek żądania, aby określić format zwracany. Gdy `Accept` nagłówek zawiera typ, który obsługuje serwer, zwracany jest ten typ. W następnej sekcji pokazano, jak dodać dodatkowe formaterów.

Akcje kontrolera mogą zwracać POCO (zwykły stary obiekt CLR). Po poco jest zwracany, środowisko wykonawcze automatycznie `ObjectResult` tworzy, który zawija obiekt. Klient pobiera sformatowany obiekt seryjny. Jeśli zwracany obiekt `null`jest `204 No Content` zwracany, zwracana jest odpowiedź.

Zwracanie typu obiektu:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

W poprzednim kodzie żądanie prawidłowego aliasu `200 OK` autora zwraca odpowiedź z danymi autora. Żądanie nieprawidłowego aliasu `204 No Content` zwraca odpowiedź.

### <a name="the-accept-header"></a>Nagłówek Zaakceptuj

*Negocjacja zawartości* odbywa `Accept` się, gdy nagłówek pojawia się w żądaniu. Gdy żądanie zawiera nagłówek akceptowania, ASP.NET Core:

* Wylicza typy nośników w nagłówku accept w kolejności preferencji.
* Próbuje znaleźć formater, który może uzyskać odpowiedź w jednym z formatów określonych.

Jeśli nie zostanie znaleziony żaden program formater, który może spełnić żądanie klienta, ASP.NET Core:

* `406 Not Acceptable` Zwraca, <xref:Microsoft.AspNetCore.Mvc.MvcOptions> jeśli został ustawiony lub -
* Próbuje znaleźć pierwszy program formatu, który może spowodować odpowiedź.

Jeśli dla żądanego formatu nie skonfigurowano żadnego formatera, używany jest pierwszy formater, który może sformatować obiekt. Jeśli `Accept` w żądaniu nie ma nagłówka:

* Pierwszy formater, który może obsługiwać obiekt jest używany do serializacji odpowiedzi.
* Nie ma żadnych negocjacji. Serwer określa, jaki format ma zwrócić.

Jeśli nagłówek Akceptuj `*/*`zawiera , nagłówek `RespectBrowserAcceptHeader` jest ignorowany, <xref:Microsoft.AspNetCore.Mvc.MvcOptions>chyba że jest ustawiona na true on .

### <a name="browsers-and-content-negotiation"></a>Przeglądarki i negocjacje treści

W przeciwieństwie do typowych `Accept` klientów interfejsu API przeglądarki internetowe dostarczają nagłówki. Przeglądarka internetowa określić wiele formatów, w tym symbole wieloznaczne. Domyślnie, gdy struktura wykryje, że żądanie pochodzi z przeglądarki:

* Nagłówek `Accept` jest ignorowany.
* Zawartość jest zwracana w JSON, chyba że skonfigurowano inaczej.

Zapewnia to bardziej spójne środowisko w przeglądarkach podczas korzystania z interfejsów API.

Aby skonfigurować aplikację do honorujskusje nagłówki akceptujące przeglądarki, ustaw na: <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> `true`

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a>Konfigurowanie programów formatu

Aplikacje, które muszą obsługiwać dodatkowe formaty można dodać odpowiednie pakiety NuGet i skonfigurować obsługę. Istnieją oddzielne formatery dla wejścia i wyjścia. Formaterów wprowadzania są używane przez [powiązanie modelu](xref:mvc/models/model-binding). Formaterów danych wyjściowych są używane do formatowania odpowiedzi. Aby uzyskać informacje dotyczące tworzenia niestandardowego formatera, zobacz [Programy formatujące niestandardowe](xref:web-api/advanced/custom-formatters).

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a>Dodawanie obsługi formatów XML

Formaterów XML zaimplementowanych przy użyciu <xref:System.Xml.Serialization.XmlSerializer> są konfigurowane przez wywołanie: <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

Poprzedni kod serializuje wyniki `XmlSerializer`za pomocą programu .

Korzystając z poprzedniego kodu, metody kontrolera zwracają odpowiedni format `Accept` na podstawie nagłówka żądania.

### <a name="configure-systemtextjson-based-formatters"></a>Konfigurowanie programów formaterów opartych na systemie.Text.Json

Funkcje `System.Text.Json`programów formaterów opartych `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`na programach można konfigurować za pomocą programu .

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.JsonSerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.JsonSerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

Opcje serializacji wyjściowej, na podstawie akcji, można `JsonResult`skonfigurować za pomocą programu . Przykład:

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        options.WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a>Dodaj obsługę formatu JSON opartego na newtonsoft.Json

Przed ASP.NET Core 3.0 domyślnie używane formaterów JSON zaimplementowane przy użyciu `Newtonsoft.Json` pakietu. W ASP.NET Core 3.0 lub nowszym oparte są na `System.Text.Json`domyślnych formaterach JSON. Obsługa `Newtonsoft.Json` opartych formaterów i funkcji jest dostępna po zainstalowaniu pakietu [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet i skonfigurowaniu go w `Startup.ConfigureServices`programie .

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

Niektóre funkcje mogą `System.Text.Json`nie działać dobrze z programami `Newtonsoft.Json`formatującym opartymi na podstawie i wymagają odwołania do programów formatnicznych opartych na tym. Kontynuuj korzystanie `Newtonsoft.Json`z programów formatniczych opartych na aplikacji:

* Używa `Newtonsoft.Json` atrybutów. Na przykład: `[JsonProperty]` lub `[JsonIgnore]`.
* Dostosowuje ustawienia serializacji.
* Opiera się `Newtonsoft.Json` na funkcjach, które zapewnia.
* Konfiguruje `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`. Przed ASP.NET Core 3.0 akceptuje `JsonResult.SerializerSettings` `JsonSerializerSettings` wystąpienie, które jest `Newtonsoft.Json`specyficzne dla .
* Generuje dokumentację [OpenAPI.](<xref:tutorials/web-api-help-pages-using-swagger>)

Funkcje `Newtonsoft.Json`programów formaterów opartych `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`na programach można konfigurować za pomocą:

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerSettings.Converters.Add(new MyCustomJsonConverter());
});
```

Opcje serializacji wyjściowej, na podstawie akcji, można `JsonResult`skonfigurować za pomocą programu . Przykład:

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerSettings
    {
        options.Formatting = Formatting.Indented,
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

### <a name="add-xml-format-support"></a>Dodawanie obsługi formatów XML

Formatowanie XML wymaga pakietu [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet.

Formaterów XML zaimplementowanych przy użyciu <xref:System.Xml.Serialization.XmlSerializer> są konfigurowane przez wywołanie: <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

Poprzedni kod serializuje wyniki `XmlSerializer`za pomocą programu .

Korzystając z poprzedniego kodu, metody kontrolera powinny zwracać odpowiedni `Accept` format na podstawie nagłówka żądania.

::: moniker-end

### <a name="specify-a-format"></a>Określanie formatu

Aby ograniczyć formaty odpowiedzi, zastosuj [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filtr. Podobnie jak większość `[Produces]` [filtrów,](xref:mvc/controllers/filters)można zastosować w zakresie akcji, kontrolera lub zakresu globalnego:

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

Poprzedni [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filtr:

* Wymusza wszystkie akcje w kontrolerze, aby zwrócić odpowiedzi w formacie JSON.
* Jeśli inne formaterów są skonfigurowane i klient określa inny format, JSON jest zwracany.

Aby uzyskać więcej informacji, zobacz [Filtry](xref:mvc/controllers/filters).

### <a name="special-case-formatters"></a>Formaterów specjalnych przypadków

Niektóre specjalne przypadki są implementowane przy użyciu wbudowanych formaterów. Domyślnie `string` typy zwracane są formatowane jako *tekst/zwykły* *(tekst/html,* jeśli jest to wymagane za pośrednictwem nagłówka). `Accept` To zachowanie można usunąć, <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>usuwając plik . Formaterów są usuwane w metodzie. `ConfigureServices` Akcje, które mają typ `204 No Content` zwracany `null`typu obiektu modelu, zwracają po zwróceniu . To zachowanie można usunąć, <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>usuwając plik . Poniższy kod usuwa `StringOutputFormatter` `HttpNoContentOutputFormatter`i .

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

Bez `StringOutputFormatter`, wbudowane formaty formatów `string` JSON zwraca typy. Jeśli wbudowany formater JSON zostanie usunięty i dostępny jest formater XML, `string` formaty formatów XML zwracają typy. W `string` przeciwnym razie `406 Not Acceptable`zwracane są typy zwracane .

Bez `HttpNoContentOutputFormatter`, null obiekty są formatowane za pomocą skonfigurowany formater. Przykład:

* Formater JSON zwraca odpowiedź z `null`treścią .
* Formater XML zwraca pusty element XML `xsi:nil="true"` z zestawem atrybutów.

## <a name="response-format-url-mappings"></a>Mapowania adresów URL formatu odpowiedzi

Klienci mogą zażądać określonego formatu jako części adresu URL, na przykład:

* W ciągu zapytania lub części ścieżki.
* Za pomocą rozszerzenia pliku specyficzne dla formatu, takich jak xml lub .json.

Mapowanie ze ścieżki żądania powinny być określone w trasie, którą używa interfejs API. Przykład:

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

Poprzednia trasa umożliwia określony żądany format jako opcjonalne rozszerzenie pliku. Atrybut [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) sprawdza istnienie wartości formatu w `RouteData` formacie i mapuje format odpowiedzi do odpowiedniego formatera podczas tworzenia odpowiedzi.

|           Trasa        |             Program formatujący              |
|------------------------|------------------------------------|
|   `/api/products/5`    |    Domyślny formater danych wyjściowych    |
| `/api/products/5.json` | Formater JSON (jeśli jest skonfigurowany) |
| `/api/products/5.xml`  | Formater XML (jeśli jest skonfigurowany)  |
