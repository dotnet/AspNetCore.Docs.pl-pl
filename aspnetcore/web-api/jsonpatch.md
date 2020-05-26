---
title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

---

# <a name="jsonpatch-in-aspnet-core-web-api"></a>JsonPatch w interfejsie Web API ASP.NET Core

Autorzy [Dykstra](https://github.com/tdykstra) i [Kirka Larkin](https://github.com/serpent5)

::: moniker range=">= aspnetcore-3.0"

W tym artykule wyjaśniono, jak obsłużyć żądania poprawek w formacie JSON w ASP.NET Core interfejsie API sieci Web.

## <a name="package-installation"></a>Instalacja pakietu

Aby włączyć obsługę poprawek JSON w aplikacji, wykonaj następujące czynności:

1. Zainstaluj [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) pakiet NuGet.
1. Zaktualizuj `Startup.ConfigureServices` metodę projektu w celu wywołania <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*> . Przykład:

    ```csharp
    services
        .AddControllersWithViews()
        .AddNewtonsoftJson();
    ```

`AddNewtonsoftJson`jest zgodny z metodami rejestracji usługi MVC:

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers*>

## <a name="json-patch-addnewtonsoftjson-and-systemtextjson"></a>Poprawka JSON, AddNewtonsoftJson i system. Text. JSON

`AddNewtonsoftJson`zastępuje utworzone w oparciu o dane `System.Text.Json` wejściowe i wyjściowe elementy formatujące używane do formatowania **całej** zawartości JSON. Aby dodać obsługę poprawki JSON przy użyciu `Newtonsoft.Json` , pozostawiając inne elementy formatujące bez zmian, zaktualizuj `Startup.ConfigureServices` metodę projektu w następujący sposób:

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet)]

Poprzedzający kod wymaga `Microsoft.AspNetCore.Mvc.NewtonsoftJson` pakietu i następujących `using` instrukcji:

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet1)]

## <a name="patch-http-request-method"></a>Poprawka metody żądania HTTP

Metody PUT i [patch](https://tools.ietf.org/html/rfc5789) są używane do aktualizowania istniejącego zasobu. Różnica między nimi polega na tym, że zastępuje cały zasób, podczas gdy poprawka określa tylko te zmiany.

## <a name="json-patch"></a>Poprawka JSON

[Poprawka JSON](https://tools.ietf.org/html/rfc6902) to format służący do określania aktualizacji, które mają zostać zastosowane do zasobu. Dokument poprawki JSON zawiera tablicę *operacji*. Każda operacja identyfikuje określony typ zmiany. Przykłady takich zmian obejmują dodanie elementu tablicy lub zastępowanie wartości właściwości.

Na przykład następujące dokumenty JSON reprezentują zasób, dokument poprawki JSON dla zasobu oraz wynik zastosowania operacji patch.

### <a name="resource-example"></a>Przykład zasobu

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a>Przykład poprawki JSON

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

W powyższym formacie JSON:

* `op`Właściwość wskazuje typ operacji.
* `path`Właściwość wskazuje element do zaktualizowania.
* `value`Właściwość zawiera nową wartość.

### <a name="resource-after-patch"></a>Zasób po zastosowaniu poprawki

Poniżej znajduje się zasób po zastosowaniu poprzedniego dokumentu poprawki JSON:

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

Zmiany wprowadzone przez zastosowanie dokumentu poprawki JSON do zasobu są niepodzielne. Jeśli jakakolwiek operacja na liście nie powiedzie się, nie zostanie zastosowana żadna operacja na liście.

## <a name="path-syntax"></a>Składnia ścieżki

Właściwość [Path](https://tools.ietf.org/html/rfc6901) obiektu operacji ma ukośniki między poziomami. Na przykład `"/address/zipCode"`.

W celu określenia elementów tablicy są używane indeksy oparte na wartości zero. Pierwszy element `addresses` tablicy będzie miał wartość `/addresses/0` . Na `add` koniec tablicy Użyj łącznika ( `-` ), a nie numeru indeksu: `/addresses/-` .

### <a name="operations"></a>Operacje

W poniższej tabeli przedstawiono obsługiwane operacje zgodnie z definicją w [specyfikacji poprawek JSON](https://tools.ietf.org/html/rfc6902):

|Operacja  | Uwagi |
|---
title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

------|---
title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

----------------| | `add`     | Dodaj właściwość lub element tablicy. Dla istniejącej właściwości: Ustaw wartość. | | `remove`  | Usuń właściwość lub element tablicy. | | `replace` | Takie same, jak `remove` następuje `add` w tej samej lokalizacji. | | `move`    | Takie samo jak `remove` ze źródła, a następnie `add` do miejsca docelowego przy użyciu wartości ze źródła. | | `copy`    | Takie samo jak `add` miejsce docelowe przy użyciu wartości ze źródła. | | `test`    | Zwróć kod stanu sukcesu, jeśli wartość jest równa `path` = podano `value` . |

## <a name="json-patch-in-aspnet-core"></a>Poprawka JSON w ASP.NET Core

ASP.NET Core implementacja poprawki JSON jest dostępna w pakiecie NuGet [Microsoft. AspNetCore. JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) .

## <a name="action-method-code"></a>Kod metody akcji

W kontrolerze interfejsu API Metoda akcji dla poprawki JSON:

* Ma adnotację z `HttpPatch` atrybutem.
* Akceptuje element `JsonPatchDocument<T>` , zazwyczaj z `[FromBody]` .
* Wywołuje `ApplyTo` dokument poprawki, aby zastosować zmiany.

Oto przykład:

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

Ten kod z przykładowej aplikacji współdziała z następującym `Customer` modelem:

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

Przykładowa Metoda akcji:

* Konstruuje a `Customer` .
* Stosuje poprawkę.
* Zwraca wynik w treści odpowiedzi.

W rzeczywistej aplikacji kod pobiera dane z magazynu, takiego jak baza danych, i aktualizuje bazę danych po zastosowaniu poprawki.

### <a name="model-state"></a>Stan modelu

Poprzednia metoda akcji przykład wywołuje Przeciążenie `ApplyTo` , które ma stan modelu jako jeden z jego parametrów. W przypadku tej opcji można odbierać komunikaty o błędach w odpowiedziach. Poniższy przykład przedstawia treść nieprawidłowej odpowiedzi na żądanie 400 dla `test` operacji:

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a>Obiekty dynamiczne

W poniższym przykładzie metody akcji pokazano, jak zastosować poprawkę do obiektu dynamicznego:

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a>Operacja dodawania

* Jeśli `path` wskazuje element tablicy: wstawia nowy element przed określony przez `path` .
* Jeśli `path` wskazuje Właściwość: ustawia wartość właściwości.
* Jeśli `path` wskazuje nieistniejącą lokalizację:
  * Jeśli zasób do poprawki jest obiektem dynamicznym: dodaje właściwość.
  * Jeśli zasób do poprawki jest obiektem statycznym: żądanie nie powiedzie się.

Następujący przykładowy dokument poprawek ustawia wartość `CustomerName` i dodaje `Order` obiekt na końcu `Orders` tablicy.

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a>Operacja usuwania

* Jeśli `path` wskazuje element tablicy: usuwa element.
* Jeśli `path` wskazuje Właściwość:
  * Jeśli zasób do poprawki jest obiektem dynamicznym: usuwa właściwość.
  * Jeśli zasób do poprawki jest obiektem statycznym:
    * Jeśli właściwość dopuszcza wartość null: ustawia ją na wartość null.
    * Jeśli właściwość nie dopuszcza wartości null, ustawia ją na `default<T>` .

Następujący Przykładowa poprawka zestawów dokumentów `CustomerName` do wartości null i usuwa `Orders[0]` :

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a>Operacja zamiany

Ta operacja jest funkcjonalnie taka sama jak `remove` po nim `add` .

Następujący przykładowy dokument poprawek ustawia wartość `CustomerName` i zastępuje ją `Orders[0]` nowym `Order` obiektem:

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a>Operacja przenoszenia

* Jeśli `path` wskazuje element tablicy: kopiuje `from` element do lokalizacji `path` elementu, a następnie uruchamia `remove` operację na `from` elemencie.
* Jeśli `path` wskazuje Właściwość: kopiuje wartość `from` właściwości do `path` właściwości, a następnie uruchamia `remove` operację na `from` właściwości.
* Jeśli `path` wskazuje na nieistniejącą Właściwość:
  * Jeśli zasób do poprawki jest obiektem statycznym: żądanie nie powiedzie się.
  * Jeśli zasób do poprawki jest obiektem dynamicznym: kopiuje `from` Właściwość do lokalizacji wskazywanej przez `path` , a następnie uruchamia `remove` operację na `from` właściwości.

Następujący przykładowy dokument poprawek:

* Kopiuje wartość `Orders[0].OrderName` do `CustomerName` .
* Ustawia `Orders[0].OrderName` wartość null.
* Przenosi `Orders[1]` do przed `Orders[0]` .

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a>Operacja kopiowania

Ta operacja jest funkcjonalnie taka sama jak `move` operacja bez `remove` kroku końcowego.

Następujący przykładowy dokument poprawek:

* Kopiuje wartość `Orders[0].OrderName` do `CustomerName` .
* Wstawia kopię `Orders[1]` przed `Orders[0]` .

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a>Operacja testowa

Jeśli wartość w lokalizacji wskazywanej przez różni `path` się od wartości podanej w `value` , żądanie kończy się niepowodzeniem. W takim przypadku całe żądanie PATCH kończy się niepowodzeniem, nawet jeśli wszystkie inne operacje w dokumencie poprawki zakończyły się powodzeniem.

`test`Operacja jest często używana do zapobiegania aktualizacji, gdy występuje konflikt współbieżności.

Następujący przykładowy dokument poprawek nie ma wpływu, jeśli początkowa wartość `CustomerName` to "Jan", ponieważ test zakończy się niepowodzeniem:

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a>Uzyskiwanie kodu

[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples). ([Jak pobrać](xref:index#how-to-download-a-sample)).

Aby przetestować przykład, uruchom aplikację i Wyślij żądania HTTP z następującymi ustawieniami:

* Adres URL:`http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`
* Metoda HTTP:`PATCH`
* Nagłówki`Content-Type: application/json-patch+json`
* Treść: Skopiuj i wklej jeden z przykładów dokumentu poprawki JSON z folderu projektu *JSON* .

## <a name="additional-resources"></a>Zasoby dodatkowe

* [IETF RFC 5789 Specyfikacja metody poprawek](https://tools.ietf.org/html/rfc5789)
* [IETF RFC 6902 — Specyfikacja poprawek JSON](https://tools.ietf.org/html/rfc6902)
* [IETF RFC 6901 JSON Format ścieżki poprawek](https://tools.ietf.org/html/rfc6901)
* [Dokumentacja poprawek JSON](https://jsonpatch.com/). Zawiera linki do zasobów do tworzenia dokumentów poprawek JSON.
* [ASP.NET Core kod źródłowy poprawki JSON](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

W tym artykule wyjaśniono, jak obsłużyć żądania poprawek w formacie JSON w ASP.NET Core interfejsie API sieci Web.

## <a name="patch-http-request-method"></a>Poprawka metody żądania HTTP

Metody PUT i [patch](https://tools.ietf.org/html/rfc5789) są używane do aktualizowania istniejącego zasobu. Różnica między nimi polega na tym, że zastępuje cały zasób, podczas gdy poprawka określa tylko te zmiany.

## <a name="json-patch"></a>Poprawka JSON

[Poprawka JSON](https://tools.ietf.org/html/rfc6902) to format służący do określania aktualizacji, które mają zostać zastosowane do zasobu. Dokument poprawki JSON zawiera tablicę *operacji*. Każda operacja identyfikuje określony typ zmiany, na przykład Dodaj element tablicy lub Zastąp wartość właściwości.

Na przykład następujące dokumenty JSON reprezentują zasób, dokument poprawki JSON dla zasobu oraz wynik zastosowania operacji patch.

### <a name="resource-example"></a>Przykład zasobu

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a>Przykład poprawki JSON

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

W powyższym formacie JSON:

* `op`Właściwość wskazuje typ operacji.
* `path`Właściwość wskazuje element do zaktualizowania.
* `value`Właściwość zawiera nową wartość.

### <a name="resource-after-patch"></a>Zasób po zastosowaniu poprawki

Poniżej znajduje się zasób po zastosowaniu poprzedniego dokumentu poprawki JSON:

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

Zmiany wprowadzone przez zastosowanie dokumentu poprawek JSON do zasobu są niepodzielne: Jeśli jakakolwiek operacja na liście nie powiedzie się, nie zostanie zastosowana żadna operacja na liście.

## <a name="path-syntax"></a>Składnia ścieżki

Właściwość [Path](https://tools.ietf.org/html/rfc6901) obiektu operacji ma ukośniki między poziomami. Na przykład `"/address/zipCode"`.

W celu określenia elementów tablicy są używane indeksy oparte na wartości zero. Pierwszy element `addresses` tablicy będzie miał wartość `/addresses/0` . Na `add` koniec tablicy Użyj łącznika (-), a nie numeru indeksu: `/addresses/-` .

### <a name="operations"></a>Operacje

W poniższej tabeli przedstawiono obsługiwane operacje zgodnie z definicją w [specyfikacji poprawek JSON](https://tools.ietf.org/html/rfc6902):

|Operacja  | Uwagi |
|---
title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

------|---
title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

----------------| | `add`     | Dodaj właściwość lub element tablicy. Dla istniejącej właściwości: Ustaw wartość. | | `remove`  | Usuń właściwość lub element tablicy. | | `replace` | Takie same, jak `remove` następuje `add` w tej samej lokalizacji. | | `move`    | Takie samo jak `remove` ze źródła, a następnie `add` do miejsca docelowego przy użyciu wartości ze źródła. | | `copy`    | Takie samo jak `add` miejsce docelowe przy użyciu wartości ze źródła. | | `test`    | Zwróć kod stanu sukcesu, jeśli wartość jest równa `path` = podano `value` . |

## <a name="jsonpatch-in-aspnet-core"></a>JsonPatch w ASP.NET Core

ASP.NET Core implementacja poprawki JSON jest dostępna w pakiecie NuGet [Microsoft. AspNetCore. JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) . Pakiet jest zawarty w pakiecie [Microsoft. AspnetCore. app](xref:fundamentals/metapackage-app) .

## <a name="action-method-code"></a>Kod metody akcji

W kontrolerze interfejsu API Metoda akcji dla poprawki JSON:

* Ma adnotację z `HttpPatch` atrybutem.
* Akceptuje element `JsonPatchDocument<T>` , zazwyczaj z `[FromBody]` .
* Wywołuje `ApplyTo` dokument poprawki, aby zastosować zmiany.

Oto przykład:

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

Ten kod z przykładowej aplikacji współdziała z następującym `Customer` modelem.

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

Przykładowa Metoda akcji:

* Konstruuje a `Customer` .
* Stosuje poprawkę.
* Zwraca wynik w treści odpowiedzi.

 W rzeczywistej aplikacji kod pobiera dane z magazynu, takiego jak baza danych, i aktualizuje bazę danych po zastosowaniu poprawki.

### <a name="model-state"></a>Stan modelu

Poprzednia metoda akcji przykład wywołuje Przeciążenie `ApplyTo` , które ma stan modelu jako jeden z jego parametrów. W przypadku tej opcji można odbierać komunikaty o błędach w odpowiedziach. Poniższy przykład przedstawia treść nieprawidłowej odpowiedzi na żądanie 400 dla `test` operacji:

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a>Obiekty dynamiczne

W poniższym przykładzie metody akcji pokazano, jak zastosować poprawkę do obiektu dynamicznego.

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a>Operacja dodawania

* Jeśli `path` wskazuje element tablicy: wstawia nowy element przed określony przez `path` .
* Jeśli `path` wskazuje Właściwość: ustawia wartość właściwości.
* Jeśli `path` wskazuje nieistniejącą lokalizację:
  * Jeśli zasób do poprawki jest obiektem dynamicznym: dodaje właściwość.
  * Jeśli zasób do poprawki jest obiektem statycznym: żądanie nie powiedzie się.

Następujący przykładowy dokument poprawek ustawia wartość `CustomerName` i dodaje `Order` obiekt na końcu `Orders` tablicy.

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a>Operacja usuwania

* Jeśli `path` wskazuje element tablicy: usuwa element.
* Jeśli `path` wskazuje Właściwość:
  * Jeśli zasób do poprawki jest obiektem dynamicznym: usuwa właściwość.
  * Jeśli zasób do poprawki jest obiektem statycznym:
    * Jeśli właściwość dopuszcza wartość null: ustawia ją na wartość null.
    * Jeśli właściwość nie dopuszcza wartości null, ustawia ją na `default<T>` .

Poniższy Przykładowa poprawka zestawów dokumentów `CustomerName` do wartości null i usuwa `Orders[0]` .

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a>Operacja zamiany

Ta operacja jest funkcjonalnie taka sama jak `remove` po nim `add` .

Następujący przykładowy dokument poprawek ustawia wartość `CustomerName` i zastępuje ją `Orders[0]` nowym `Order` obiektem.

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a>Operacja przenoszenia

* Jeśli `path` wskazuje element tablicy: kopiuje `from` element do lokalizacji `path` elementu, a następnie uruchamia `remove` operację na `from` elemencie.
* Jeśli `path` wskazuje Właściwość: kopiuje wartość `from` właściwości do `path` właściwości, a następnie uruchamia `remove` operację na `from` właściwości.
* Jeśli `path` wskazuje na nieistniejącą Właściwość:
  * Jeśli zasób do poprawki jest obiektem statycznym: żądanie nie powiedzie się.
  * Jeśli zasób do poprawki jest obiektem dynamicznym: kopiuje `from` Właściwość do lokalizacji wskazywanej przez `path` , a następnie uruchamia `remove` operację na `from` właściwości.

Następujący przykładowy dokument poprawek:

* Kopiuje wartość `Orders[0].OrderName` do `CustomerName` .
* Ustawia `Orders[0].OrderName` wartość null.
* Przenosi `Orders[1]` do przed `Orders[0]` .

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a>Operacja kopiowania

Ta operacja jest funkcjonalnie taka sama jak `move` operacja bez `remove` kroku końcowego.

Następujący przykładowy dokument poprawek:

* Kopiuje wartość `Orders[0].OrderName` do `CustomerName` .
* Wstawia kopię `Orders[1]` przed `Orders[0]` .

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a>Operacja testowa

Jeśli wartość w lokalizacji wskazywanej przez różni `path` się od wartości podanej w `value` , żądanie kończy się niepowodzeniem. W takim przypadku całe żądanie PATCH kończy się niepowodzeniem, nawet jeśli wszystkie inne operacje w dokumencie poprawki zakończyły się powodzeniem.

`test`Operacja jest często używana do zapobiegania aktualizacji, gdy występuje konflikt współbieżności.

Następujący przykładowy dokument poprawek nie ma wpływu, jeśli początkowa wartość `CustomerName` to "Jan", ponieważ test zakończy się niepowodzeniem:

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a>Uzyskiwanie kodu

[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2). ([Jak pobrać](xref:index#how-to-download-a-sample)).

Aby przetestować przykład, uruchom aplikację i Wyślij żądania HTTP z następującymi ustawieniami:

* Adres URL:`http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`
* Metoda HTTP:`PATCH`
* Nagłówki`Content-Type: application/json-patch+json`
* Treść: Skopiuj i wklej jeden z przykładów dokumentu poprawki JSON z folderu projektu *JSON* .

## <a name="additional-resources"></a>Zasoby dodatkowe

* [IETF RFC 5789 Specyfikacja metody poprawek](https://tools.ietf.org/html/rfc5789)
* [IETF RFC 6902 — Specyfikacja poprawek JSON](https://tools.ietf.org/html/rfc6902)
* [IETF RFC 6901 JSON Format ścieżki poprawek](https://tools.ietf.org/html/rfc6901)
* [Dokumentacja poprawek JSON](https://jsonpatch.com/). Zawiera linki do zasobów do tworzenia dokumentów poprawek JSON.
* [ASP.NET Core kod źródłowy poprawki JSON](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end
