---
title: JsonPatch w ASP.NET Core web API
author: rick-anderson
description: Dowiedz się, jak obsługiwać żądania poprawek JSON w internetowym interfejsie API ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/02/2020
uid: web-api/jsonpatch
ms.openlocfilehash: be4115e870dac818aeb6b1e65ddfb21e89d9cf25
ms.sourcegitcommit: 9675db7bf4b67ae269f9226b6f6f439b5cce4603
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80625880"
---
# <a name="jsonpatch-in-aspnet-core-web-api"></a>JsonPatch w ASP.NET Core web API

Przez [Tom Dykstra](https://github.com/tdykstra) i [Kirk Larkin](https://github.com/serpent5)

::: moniker range=">= aspnetcore-3.0"

W tym artykule wyjaśniono, jak obsługiwać żądania poprawek JSON w interfejsie API sieci web ASP.NET Core.

## <a name="package-installation"></a>Instalacja pakietu

Aby włączyć obsługę poprawek JSON w aplikacji, wykonaj następujące czynności:

1. Zainstaluj pakiet [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet.
1. Zaktualizuj `Startup.ConfigureServices` metodę <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*>projektu, aby wywołać . Przykład:

    ```csharp
    services
        .AddControllersWithViews()
        .AddNewtonsoftJson();
    ```

`AddNewtonsoftJson`jest kompatybilny z metodami rejestracji usługi MVC:

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers*>

## <a name="json-patch-addnewtonsoftjson-and-systemtextjson"></a>Łatka JSON, AddNewtonsoftJson i System.Text.Json

`AddNewtonsoftJson`zastępuje `System.Text.Json`formatery wejściowe i wyjściowe oparte na podstawie formatowania używane do formatowania **całej** zawartości JSON. Aby dodać obsługę poprawki `Newtonsoft.Json`JSON przy użyciu , pozostawiając inne formaterów `Startup.ConfigureServices` bez zmian, zaktualizuj metodę projektu w następujący sposób:

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet)]

Poprzedni kod wymaga `Microsoft.AspNetCore.Mvc.NewtonsoftJson` pakietu i `using` następujące instrukcje:

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet1)]

## <a name="patch-http-request-method"></a>Metoda żądania HTTP PATCH

Metody PUT i [PATCH](https://tools.ietf.org/html/rfc5789) są używane do aktualizowania istniejącego zasobu. Różnica między nimi polega na tym, że PUT zastępuje cały zasób, podczas gdy patch określa tylko zmiany.

## <a name="json-patch"></a>Łatka JSON

[JSON Patch](https://tools.ietf.org/html/rfc6902) to format określania aktualizacji, które mają być stosowane do zasobu. Dokument poprawki JSON ma tablicę *operacji*. Każda operacja identyfikuje określony typ zmiany. Przykłady takich zmian obejmują dodawanie elementu tablicy lub zastąpienie wartości właściwości.

Na przykład następujące dokumenty JSON reprezentują zasób, dokument JSON Patch dla zasobu i wynik zastosowania operacji poprawki.

### <a name="resource-example"></a>Przykład zasobu

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a>Przykład poprawki JSON

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

W poprzednim JSON:

* Właściwość `op` wskazuje typ operacji.
* Właściwość `path` wskazuje element do aktualizacji.
* Właściwość `value` zawiera nową wartość.

### <a name="resource-after-patch"></a>Zasób po aktualizacji

Oto zasób po zastosowaniu poprzedniego dokumentu JSON Patch:

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

Zmiany wprowadzone przez zastosowanie dokumentu JSON Patch do zasobu są niepodzielne. Jeśli jakakolwiek operacja na liście nie powiedzie się, nie jest stosowana żadna operacja na liście.

## <a name="path-syntax"></a>Składnia ścieżki

Właściwość [path](https://tools.ietf.org/html/rfc6901) obiektu operacji ma ukośniki między poziomami. Na przykład `"/address/zipCode"`.

Indeksy oparte na wartościach zerowych są używane do określania elementów tablicy. Pierwszy element tablicy `addresses` będzie `/addresses/0`w . Aby `add` zakończyć tablicę, należy użyć`-`łącznika ( ) `/addresses/-`zamiast numeru indeksu: .

### <a name="operations"></a>Operacje

W poniższej tabeli przedstawiono obsługiwane operacje zdefiniowane w [specyfikacji JSON Patch:](https://tools.ietf.org/html/rfc6902)

|Operacja  | Uwagi |
|-----------|--------------------------------|
| `add`     | Dodaj właściwość lub element tablicy. Dla istniejącej właściwości: ustaw wartość.|
| `remove`  | Usuń właściwość lub element tablicy. |
| `replace` | Tak `remove` samo `add` jak w tym samym miejscu. |
| `move`    | Tak `remove` samo jak `add` ze źródła, po którym następuje miejsce docelowe przy użyciu wartości ze źródła. |
| `copy`    | Tak `add` samo jak w przypadku miejsca docelowego przy użyciu wartości ze źródła. |
| `test`    | Zwraca kod stanu sukcesu, jeśli wartość w `path` = pod warunkiem `value`.|

## <a name="json-patch-in-aspnet-core"></a>Łatka JSON w ASP.NET Core

Implementacja ASP.NET Core poprawki JSON jest dostępna w pakiecie [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet.

## <a name="action-method-code"></a>Kod metody akcji

W kontrolerze interfejsu API: metoda akcji dla poprawki JSON:

* Jest opisywany z `HttpPatch` atrybutem.
* Akceptuje , `JsonPatchDocument<T>`zazwyczaj z `[FromBody]`.
* Wzywa `ApplyTo` dokument poprawki do zastosowania zmian.

Oto przykład:

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

Ten kod z przykładowej aplikacji `Customer` działa z następującym modelem:

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

Przykładowa metoda działania:

* Konstruuje `Customer`plik .
* Stosuje poprawkę.
* Zwraca wynik w treści odpowiedzi.

W prawdziwej aplikacji kod będzie pobierać dane z magazynu, takich jak baza danych i zaktualizować bazę danych po zastosowaniu poprawki.

### <a name="model-state"></a>Stan modelu

W poprzednim przykładzie metody akcji `ApplyTo` wywołuje przeciążenie tego przyjmuje stan modelu jako jeden z jego parametrów. Za pomocą tej opcji można otrzymywać komunikaty o błędach w odpowiedziach. Poniższy przykład przedstawia treść odpowiedzi 400 Bad `test` Request dla operacji:

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a>Obiekty dynamiczne

Poniższa metoda akcji pokazuje, jak zastosować poprawkę do obiektu dynamicznego:

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a>Operacja dodawania

* Jeśli `path` wskazuje element tablicy: wstawia nowy `path`element przed elementem określonym przez .
* Jeśli `path` wskazuje na właściwość: ustawia wartość właściwości.
* Jeśli `path` wskazuje nieistniejącą lokalizację:
  * Jeśli zasób do poprawki jest obiekt dynamiczny: dodaje właściwość.
  * Jeśli zasób do poprawki jest obiektstatyczny: żądanie kończy się niepowodzeniem.

Poniższy przykładowy dokument poprawki `CustomerName` ustawia `Order` wartość i dodaje `Orders` obiekt na końcu tablicy.

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a>Operacja usuwania

* Jeśli `path` wskazuje element tablicy: usuwa element.
* Jeśli `path` wskazuje na właściwość:
  * Jeśli zasób do poprawki jest obiekt dynamiczny: usuwa właściwość.
  * Jeśli zasób do poprawki jest obiektem statycznym:
    * Jeśli właściwość jest nullable: ustawia go na null.
    * Jeśli właściwość nie może być nullowa, ustawia ją na `default<T>`.

Następujący przykładowy dokument `CustomerName` poprawki ustawia `Orders[0]`wartość null i usuwa:

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a>Operacja wymiany

Ta operacja jest funkcjonalnie `remove` taka `add`sama jak po .

Następujący przykładowy dokument poprawki `CustomerName` ustawia `Orders[0]`wartość i `Order` zastępuje go nowym obiektem:

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a>Operacja przenoszenia

* Jeśli `path` wskazuje element tablicy: `from` kopiuje `path` element do `remove` lokalizacji elementu, a następnie uruchamia operację na elemencie. `from`
* Jeśli `path` wskazuje właściwość: `from` kopiuje `path` wartość właściwości do `remove` właściwości, `from` a następnie uruchamia operację na właściwości.
* Jeśli `path` wskazuje nieistniejącą właściwość:
  * Jeśli zasób do poprawki jest obiektstatyczny: żądanie kończy się niepowodzeniem.
  * Jeśli zasób do poprawki jest `from` obiektem dynamicznym: `path`kopiuje `remove` właściwość `from` do lokalizacji wskazanej przez program , a następnie uruchamia operację we właściwości.

Następujący przykładowy dokument poprawki:

* Kopiuje wartość `Orders[0].OrderName` `CustomerName`do .
* Ustawia `Orders[0].OrderName` wartość null.
* Przechodzi `Orders[1]` do `Orders[0]`przed .

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a>Operacja kopiowania

Ta operacja jest funkcjonalnie `move` taka sama `remove` jak operacja bez ostatniego kroku.

Następujący przykładowy dokument poprawki:

* Kopiuje wartość `Orders[0].OrderName` `CustomerName`do .
* Wstawia kopię `Orders[1]` `Orders[0]`przed .

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a>Operacja testowa

Jeśli wartość w lokalizacji wskazanej przez `path` różni się `value`od wartości podanej w , żądanie nie powiedzie się. W takim przypadku całe żądanie PATCH kończy się niepowodzeniem, nawet jeśli wszystkie inne operacje w dokumencie poprawki w przeciwnym razie zakończyłyby się pomyślnie.

Operacja `test` jest często używana, aby zapobiec aktualizacji, gdy istnieje konflikt współbieżności.

Następujący przykładowy dokument poprawki nie ma `CustomerName` wpływu, jeśli wartość początkowa jest "John", ponieważ test nie powiedzie się:

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a>Uzyskiwanie kodu

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples). (Jak[pobrać](xref:index#how-to-download-a-sample)).

Aby przetestować przykład, uruchom aplikację i wyślij żądania HTTP z następującymi ustawieniami:

* Adres url:`http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`
* Metoda HTTP:`PATCH`
* Nagłówka:`Content-Type: application/json-patch+json`
* Treść: Skopiuj i wklej jedną z próbek dokumentu poprawki JSON z folderu projektu *JSON.*

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Specyfikacja metody IETF RFC 5789 PATCH](https://tools.ietf.org/html/rfc5789)
* [IETF RFC 6902 JSON Patch specyfikacja](https://tools.ietf.org/html/rfc6902)
* [Specyfikacja formatu ścieżki poprawki IETF RFC 6901 JSON](https://tools.ietf.org/html/rfc6901)
* [Dokumentacja JSON Patch](https://jsonpatch.com/). Zawiera łącza do zasobów do tworzenia dokumentów JSON Patch.
* [ASP.NET Podstawowy kod źródłowy JSON Patch](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

W tym artykule wyjaśniono, jak obsługiwać żądania poprawek JSON w interfejsie API sieci web ASP.NET Core.

## <a name="patch-http-request-method"></a>Metoda żądania HTTP PATCH

Metody PUT i [PATCH](https://tools.ietf.org/html/rfc5789) są używane do aktualizowania istniejącego zasobu. Różnica między nimi polega na tym, że PUT zastępuje cały zasób, podczas gdy patch określa tylko zmiany.

## <a name="json-patch"></a>Łatka JSON

[JSON Patch](https://tools.ietf.org/html/rfc6902) to format określania aktualizacji, które mają być stosowane do zasobu. Dokument poprawki JSON ma tablicę *operacji*. Każda operacja identyfikuje określonego typu zmiany, takich jak dodać element tablicy lub zastąpić wartość właściwości.

Na przykład następujące dokumenty JSON reprezentują zasób, dokument poprawki JSON dla zasobu i wynik zastosowania operacji poprawki.

### <a name="resource-example"></a>Przykład zasobu

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a>Przykład poprawki JSON

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

W poprzednim JSON:

* Właściwość `op` wskazuje typ operacji.
* Właściwość `path` wskazuje element do aktualizacji.
* Właściwość `value` zawiera nową wartość.

### <a name="resource-after-patch"></a>Zasób po aktualizacji

Oto zasób po zastosowaniu poprzedniego dokumentu JSON Patch:

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

Zmiany wprowadzone przez zastosowanie dokumentu JSON Patch do zasobu są niepodzielne: jeśli jakakolwiek operacja na liście nie powiedzie się, nie zostanie zastosowana żadna operacja na liście.

## <a name="path-syntax"></a>Składnia ścieżki

Właściwość [path](https://tools.ietf.org/html/rfc6901) obiektu operacji ma ukośniki między poziomami. Na przykład `"/address/zipCode"`.

Indeksy oparte na wartościach zerowych są używane do określania elementów tablicy. Pierwszy element tablicy `addresses` będzie `/addresses/0`w . Na `add` końcu tablicy należy użyć łącznika (-), a nie `/addresses/-`numeru indeksu: .

### <a name="operations"></a>Operacje

W poniższej tabeli przedstawiono obsługiwane operacje zdefiniowane w [specyfikacji JSON Patch:](https://tools.ietf.org/html/rfc6902)

|Operacja  | Uwagi |
|-----------|--------------------------------|
| `add`     | Dodaj właściwość lub element tablicy. Dla istniejącej właściwości: ustaw wartość.|
| `remove`  | Usuń właściwość lub element tablicy. |
| `replace` | Tak `remove` samo `add` jak w tym samym miejscu. |
| `move`    | Tak `remove` samo jak `add` ze źródła, po którym następuje miejsce docelowe przy użyciu wartości ze źródła. |
| `copy`    | Tak `add` samo jak w przypadku miejsca docelowego przy użyciu wartości ze źródła. |
| `test`    | Zwraca kod stanu sukcesu, jeśli wartość w `path` = pod warunkiem `value`.|

## <a name="jsonpatch-in-aspnet-core"></a>JsonPatch w ASP.NET Core

Implementacja ASP.NET Core poprawki JSON jest dostępna w pakiecie [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet. Pakiet znajduje się w metapakiecie [Microsoft.AspnetCore.App.](xref:fundamentals/metapackage-app)

## <a name="action-method-code"></a>Kod metody akcji

W kontrolerze interfejsu API: metoda akcji dla poprawki JSON:

* Jest opisywany z `HttpPatch` atrybutem.
* Akceptuje , `JsonPatchDocument<T>`zazwyczaj z `[FromBody]`.
* Wzywa `ApplyTo` dokument poprawki do zastosowania zmian.

Oto przykład:

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

Ten kod z przykładowej aplikacji `Customer` działa z następującym modelem.

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

Przykładowa metoda działania:

* Konstruuje `Customer`plik .
* Stosuje poprawkę.
* Zwraca wynik w treści odpowiedzi.

 W prawdziwej aplikacji kod będzie pobierać dane z magazynu, takich jak baza danych i zaktualizować bazę danych po zastosowaniu poprawki.

### <a name="model-state"></a>Stan modelu

W poprzednim przykładzie metody akcji `ApplyTo` wywołuje przeciążenie tego przyjmuje stan modelu jako jeden z jego parametrów. Za pomocą tej opcji można otrzymywać komunikaty o błędach w odpowiedziach. Poniższy przykład przedstawia treść odpowiedzi 400 Bad `test` Request dla operacji:

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

* Jeśli `path` wskazuje element tablicy: wstawia nowy `path`element przed elementem określonym przez .
* Jeśli `path` wskazuje na właściwość: ustawia wartość właściwości.
* Jeśli `path` wskazuje nieistniejącą lokalizację:
  * Jeśli zasób do poprawki jest obiekt dynamiczny: dodaje właściwość.
  * Jeśli zasób do poprawki jest obiektstatyczny: żądanie kończy się niepowodzeniem.

Poniższy przykładowy dokument poprawki `CustomerName` ustawia `Order` wartość i dodaje `Orders` obiekt na końcu tablicy.

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a>Operacja usuwania

* Jeśli `path` wskazuje element tablicy: usuwa element.
* Jeśli `path` wskazuje na właściwość:
  * Jeśli zasób do poprawki jest obiekt dynamiczny: usuwa właściwość.
  * Jeśli zasób do poprawki jest obiektem statycznym:
    * Jeśli właściwość jest nullable: ustawia go na null.
    * Jeśli właściwość nie może być nullowa, ustawia ją na `default<T>`.

Poniższy przykładowy `CustomerName` dokument poprawki ustawia `Orders[0]`wartość null i usuwa .

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a>Operacja wymiany

Ta operacja jest funkcjonalnie `remove` taka `add`sama jak po .

Poniższy przykładowy dokument poprawki `CustomerName` ustawia `Orders[0]`wartość i `Order` zastępuje się nowym obiektem.

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a>Operacja przenoszenia

* Jeśli `path` wskazuje element tablicy: `from` kopiuje `path` element do `remove` lokalizacji elementu, a następnie uruchamia operację na elemencie. `from`
* Jeśli `path` wskazuje właściwość: `from` kopiuje `path` wartość właściwości do `remove` właściwości, `from` a następnie uruchamia operację na właściwości.
* Jeśli `path` wskazuje nieistniejącą właściwość:
  * Jeśli zasób do poprawki jest obiektstatyczny: żądanie kończy się niepowodzeniem.
  * Jeśli zasób do poprawki jest `from` obiektem dynamicznym: `path`kopiuje `remove` właściwość `from` do lokalizacji wskazanej przez program , a następnie uruchamia operację we właściwości.

Następujący przykładowy dokument poprawki:

* Kopiuje wartość `Orders[0].OrderName` `CustomerName`do .
* Ustawia `Orders[0].OrderName` wartość null.
* Przechodzi `Orders[1]` do `Orders[0]`przed .

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a>Operacja kopiowania

Ta operacja jest funkcjonalnie `move` taka sama `remove` jak operacja bez ostatniego kroku.

Następujący przykładowy dokument poprawki:

* Kopiuje wartość `Orders[0].OrderName` `CustomerName`do .
* Wstawia kopię `Orders[1]` `Orders[0]`przed .

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a>Operacja testowa

Jeśli wartość w lokalizacji wskazanej przez `path` różni się `value`od wartości podanej w , żądanie nie powiedzie się. W takim przypadku całe żądanie PATCH kończy się niepowodzeniem, nawet jeśli wszystkie inne operacje w dokumencie poprawki w przeciwnym razie zakończyłyby się pomyślnie.

Operacja `test` jest często używana, aby zapobiec aktualizacji, gdy istnieje konflikt współbieżności.

Następujący przykładowy dokument poprawki nie ma `CustomerName` wpływu, jeśli wartość początkowa jest "John", ponieważ test nie powiedzie się:

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a>Uzyskiwanie kodu

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2). (Jak[pobrać](xref:index#how-to-download-a-sample)).

Aby przetestować przykład, uruchom aplikację i wyślij żądania HTTP z następującymi ustawieniami:

* Adres url:`http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`
* Metoda HTTP:`PATCH`
* Nagłówka:`Content-Type: application/json-patch+json`
* Treść: Skopiuj i wklej jedną z próbek dokumentu poprawki JSON z folderu projektu *JSON.*

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Specyfikacja metody IETF RFC 5789 PATCH](https://tools.ietf.org/html/rfc5789)
* [IETF RFC 6902 JSON Patch specyfikacja](https://tools.ietf.org/html/rfc6902)
* [Specyfikacja formatu ścieżki poprawki IETF RFC 6901 JSON](https://tools.ietf.org/html/rfc6901)
* [Dokumentacja JSON Patch](https://jsonpatch.com/). Zawiera łącza do zasobów do tworzenia dokumentów JSON Patch.
* [ASP.NET Podstawowy kod źródłowy JSON Patch](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end
