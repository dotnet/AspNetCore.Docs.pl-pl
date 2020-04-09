---
title: 'Samouczek: Tworzenie internetowego interfejsu API z ASP.NET Core'
author: rick-anderson
description: Dowiedz się, jak utworzyć internetowy interfejs API za pomocą ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 2/25/2020
uid: tutorials/first-web-api
ms.openlocfilehash: 7418e962076fae3ebdbb25381838757b09046578
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417659"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a>Samouczek: Tworzenie internetowego interfejsu API z ASP.NET Core

Rick [Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5)i [Mike Wasson](https://github.com/mikewasson)

Ten samouczek uczy podstaw tworzenia internetowego interfejsu API za pomocą ASP.NET Core.

::: moniker range=">= aspnetcore-3.0"

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie projektu internetowego interfejsu API.
> * Dodaj klasę modelu i kontekst bazy danych.
> * Rusztowanie sterownika z metodami CRUD.
> * Konfigurowanie routingu, ścieżek adresów URL i wartości zwracanych.
> * Wywołanie internetowego interfejsu API za pomocą listonosza.

Na końcu masz internetowy interfejs API, który może zarządzać elementami "do wykonania" przechowywanymi w bazie danych.

## <a name="overview"></a>Omówienie

W tym samouczku utworzy się następujący interfejs API:

|interfejs API | Opis | Treść żądania | Treść odpowiedzi |
|--- | ---- | ---- | ---- |
|POBIERZ /api/TodoItems | Pobierz wszystkie przedmioty do zrobienia | Brak | Tablica elementów do wykonania|
|POBIERZ /api/TodoItems/{id} | Uzyskaj przedmiot według identyfikatora | Brak | Element do wykonania|
|POST /api/TodoItems | Dodawanie nowego elementu | Element do wykonania | Element do wykonania |
|PUT /api/TodoItems/{id} | Aktualizowanie istniejącego elementu&nbsp; | Element do wykonania | Brak |
|USUŃ /api/TodoItems/{id} &nbsp;&nbsp; | Usuwanie elementu &nbsp;&nbsp; | Brak | Brak|

Na poniższym diagramie przedstawiono projekt aplikacji.

![Klient jest reprezentowany przez pole po lewej stronie. Składa wniosek i otrzymuje odpowiedź od wniosku, pole sporządzone po prawej stronie. W polu aplikacji trzy pola reprezentują kontroler, model i warstwę dostępu do danych. Żądanie jest do kontrolera aplikacji i operacje odczytu/zapisu występują między kontrolerem a warstwą dostępu do danych. Model jest serializowany i zwracany do klienta w odpowiedzi.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a>Tworzenie projektu sieci Web

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Z menu **Plik** wybierz polecenie **Nowy** > **projekt**.
* Wybierz szablon **ASP.NET Core Web Application** i kliknij przycisk **Dalej**.
* Nazwij projekt *TodoApi* i kliknij przycisk **Utwórz**.
* W oknie **dialogowym Tworzenie nowej ASP.NET podstawowej aplikacji sieci Web** upewnij się, że wybrano opcję **.NET Core** i ASP.NET Core **3.1.** Wybierz szablon **interfejsu API** i kliknij przycisk **Utwórz**.

![Vs nowe okno dialogowe projektu](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Zmień katalogi`cd`( ) na folder, który będzie zawierał folder projektu.
* Uruchom następujące polecenia:

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* Gdy w oknie dialogowym pojawi się pytanie, czy chcesz dodać wymagane zasoby do projektu, wybierz opcję **Tak**.

  Poprzednie polecenia:

  * Tworzy nowy projekt interfejsu API sieci web i otwiera go w programie Visual Studio Code.
  * Dodaje pakiety NuGet, które są wymagane w następnej sekcji.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* Wybierz **pozycję Plik** > **nowego rozwiązania**.

  ![macOS Nowe rozwiązanie](first-web-api-mac/_static/sln.png)

* Wybierz następnie **pozycję .NET Core** > **App** > **API** > **.**

  ![macOS Nowe okno dialogowe projektu](first-web-api-mac/_static/1.png)
  
* W oknie dialogowym **Konfigurowanie nowego interfejsu API sieci Web ASP.NET Core** wybierz pozycję Target **Framework** of **.NET Core 3.1*.

* Wprowadź *pozycję TodoApi* dla **nazwy projektu,** a następnie wybierz pozycję **Utwórz**.

  ![okno dialogowe konfiguracji](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

Otwórz terminal poleceń w folderze projektu i uruchom następujące polecenia:

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a>Testowanie interfejsu API

Szablon projektu tworzy `WeatherForecast` interfejs API. Wywołanie `Get` metody z przeglądarki, aby przetestować aplikację.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Naciśnij klawisze Ctrl+F5, aby uruchomić aplikację. Visual Studio uruchamia przeglądarkę `https://localhost:<port>/WeatherForecast`i `<port>` przechodzi do , gdzie jest losowo wybrany numer portu.

Jeśli pojawi się okno dialogowe z pytaniem, czy certyfikat IIS Express ma być zaufany, wybierz opcję **Tak**. W oknie dialogowym **Ostrzeżenie o zabezpieczeniach,** które pojawi się obok, wybierz pozycję **Tak**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Naciśnij klawisze Ctrl+F5, aby uruchomić aplikację. W przeglądarce przejdź do `https://localhost:5001/WeatherForecast`następującego adresu URL: .

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

Wybierz **uruchom** > **debugowanie startowe,** aby uruchomić aplikację. Program Visual Studio dla komputerów `https://localhost:<port>`Mac `<port>` uruchamia przeglądarkę i przechodzi do miejsca, w którym jest losowo wybrany numer portu. Zwracany jest błąd HTTP 404 (nie znaleziono). Dołącz `/WeatherForecast` do adresu URL (zmień `https://localhost:<port>/WeatherForecast`adres URL na ).

---

JSON podobne do następujących jest zwracany:

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

## <a name="add-a-model-class"></a>Dodawanie klasy modelu

*Model* to zestaw klas, które reprezentują dane, którymi zarządza aplikacja. Model dla tej aplikacji `TodoItem` jest pojedynczą klasą.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* W **Eksploratorze rozwiązań**kliknij projekt prawym przyciskiem myszy. Wybierz **pozycję Dodaj** > **nowy folder**. Nazwij folder *Models*.

* Kliknij prawym przyciskiem myszy folder *Modele* i wybierz polecenie **Dodaj** > **klasę**. Nazwij klasę *TodoItem* i wybierz pozycję **Dodaj**.

* Zastąp kod szablonu następującym kodem:

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Dodawanie folderu o nazwie *Modele*.

* Dodaj `TodoItem` klasę do folderu *Models* z następującym kodem:

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* Kliknij prawym przyciskiem myszy projekt. Wybierz **pozycję Dodaj** > **nowy folder**. Nazwij folder *Models*.

  ![nowy folder](first-web-api-mac/_static/folder.png)

* Kliknij prawym przyciskiem myszy folder *Modele* i wybierz polecenie **Dodaj** > **nową** > **klasę opróżnienia ogólnego** > **Empty Class**pliku .

* Nazwij klasę *TodoItem*, a następnie kliknij przycisk **Nowy**.

* Zastąp kod szablonu następującym kodem:

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

Właściwość `Id` działa jako unikatowy klucz w relacyjnej bazie danych.

Klasy modelu można przejść w dowolnym miejscu w projekcie, ale *Models* folder jest używany przez konwencję.

## <a name="add-a-database-context"></a>Dodawanie kontekstu bazy danych

*Kontekst bazy danych* jest klasą główną, która koordynuje funkcjonalność entity framework dla modelu danych. Ta klasa jest tworzona `Microsoft.EntityFrameworkCore.DbContext` przez wyprowadzanie z klasy.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a>Dodaj serwer Microsoft.EntityFrameCore.SqlServer

* Z menu **Narzędzia** wybierz polecenie **Menedżer pakietów NuGet > Zarządzanie pakietami NuGet dla rozwiązania**.
* Wybierz kartę **Przeglądaj,** a następnie wprowadź w polu wyszukiwania pozycję **Microsoft.EntityFrameCore.SqlServer.**
* Wybierz **pozycję Microsoft.EntityFrameWorkCore.SqlServer** w lewym okienku.
* Zaznacz pole wyboru **Projekt** w prawym okienku, a następnie wybierz pozycję **Zainstaluj**.
* Użyj poprzednich instrukcji, aby `Microsoft.EntityFrameworkCore.InMemory` dodać pakiet NuGet.

![Menedżer pakietów NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a>Dodawanie kontekstu bazy danych TodoContext

* Kliknij prawym przyciskiem myszy folder *Modele* i wybierz polecenie **Dodaj** > **klasę**. Nazwij klasę *TodoContext* i kliknij przycisk **Dodaj**.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Kod programu Visual Studio / Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

* Dodaj `TodoContext` klasę do folderu *Modele.*

---

* Wprowadź następujący kod:

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a>Rejestrowanie kontekstu bazy danych

W ASP.NET Core usługi, takie jak kontekst bazy danych, muszą być zarejestrowane w kontenerze [iniekcji zależności (DI).](xref:fundamentals/dependency-injection) Kontener zapewnia usługę do kontrolerów.

Zaktualizuj *Startup.cs* następującym wyróżnionym kodem:

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

Powyższy kod ma następujące działanie:

* Usuwa nieużywane `using` deklaracje.
* Dodaje kontekst bazy danych do kontenera DI.
* Określa, że kontekst bazy danych będzie używany w bazie danych w pamięci.

## <a name="scaffold-a-controller"></a>Rusztowanie sterownika

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Kliknij prawym przyciskiem myszy folder *Kontrolery.*
* Wybierz **pozycję Dodaj** > **nowy element szkieletu**.
* Wybierz **kontroler interfejsu API z akcjami, używając entity framework**, a następnie wybierz pozycję **Dodaj**.
* W oknie dialogowym **Dodawanie kontrolera interfejsu API z akcjami przy użyciu** programu Entity Framework:

  * Wybierz **todoItem (TodoApi.Models)** w **klasie Model**.
  * Wybierz **todoContext (TodoApi.Models)** w **klasie kontekstu danych**.
  * Wybierz pozycję **Dodaj**.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Kod programu Visual Studio / Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

Uruchom następujące polecenia:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

Poprzednie polecenia:

* Dodaj pakiety NuGet wymagane dla rusztowania.
* Montuje silnik rusztowania`dotnet-aspnet-codegenerator`( ).
* Rusztowania `TodoItemsController`.

---

Wygenerowany kod:

* Oznacza klasę atrybutem. [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) Ten atrybut wskazuje, że kontroler odpowiada na żądania interfejsu API sieci web. Aby uzyskać informacje o określonych zachowaniach, <xref:web-api/index>które umożliwia atrybut, zobacz .
* Używa DI, aby wstrzyknąć kontekst bazy danych (`TodoContext`) do kontrolera. Kontekst bazy danych jest używany w każdej z metod [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) w kontrolerze.

Szablony ASP.NET Core dla:

* Kontrolery z `[action]` widokami zawierają w szablonie trasy.
* Kontrolery interfejsu API `[action]` nie zawierają w szablonie trasy.

Gdy `[action]` tokenu nie ma w szablonie trasy, nazwa [akcji](xref:mvc/controllers/routing#action) jest wykluczona z trasy. Oznacza to, że nazwa skojarzonej metody akcji nie jest używana w pasującej marszrutie.

## <a name="examine-the-posttodoitem-create-method"></a>Sprawdź PostTodoItem create metody

Zastąp return `PostTodoItem` instrukcji w aby użyć [nameof](/dotnet/csharp/language-reference/operators/nameof) operatora:

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

Poprzedni kod jest http post metody, zgodnie [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) z atrybutem. Metoda pobiera wartość elementu do wykonania z treści żądania HTTP.

Metoda: <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>

* Zwraca kod stanu HTTP 201, jeśli zakończy się pomyślnie. HTTP 201 jest standardową odpowiedzią dla metody HTTP POST, która tworzy nowy zasób na serwerze.
* Dodaje nagłówek [lokalizacja](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) do odpowiedzi. Nagłówek `Location` określa [identyfikator URI](https://developer.mozilla.org/docs/Glossary/URI) nowo utworzonego elementu do wykonania. Aby uzyskać więcej informacji, zobacz [10.2.2 201 Utworzono](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).
* Odwołuje się `GetTodoItem` do akcji, aby utworzyć `Location` identyfikator URI nagłówka. C# `nameof` słowo kluczowe jest używany w celu uniknięcia `CreatedAtAction` twardego kodowania nazwę akcji w wywołaniu.

### <a name="install-postman"></a>Instalowanie listonosza

Ten samouczek używa postmana do testowania internetowego interfejsu API.

* Instalowanie [listonosza](https://www.getpostman.com/downloads/)
* Uruchom aplikację internetową.
* Rozpocznij postman.
* Wyłączanie **weryfikacji certyfikatu SSL**
  * W **obszarze** > **Ustawienia** plików (karta**Ogólne)** wyłącz **weryfikację certyfikatu SSL**.
    > [!WARNING]
    > Ponownie włącz weryfikację certyfikatu SSL po przetestowaniu sterownika.

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a>Test PostTodoItem z listonoszem

* Utwórz nowe żądanie.
* Ustaw metodę HTTP `POST`na .
* Wybierz kartę **Treść**.
* Wybierz **nieprzetworzony** przycisk radiowy.
* Ustaw typ na **JSON (application/json)**.
* W treści żądania wprowadź JSON dla elementu do wykonania:

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* Wybierz pozycję **Wyślij**.

  ![Listonosz z żądaniem utworzenia](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a>Testowanie identyfikatora URI nagłówka lokalizacji

* Wybierz kartę **Nagłówki** w okienku **Odpowiedzi.**
* Skopiuj wartość **nagłówka Lokalizacja:**

  ![Karta Nagłówki konsoli Listonosz](first-web-api/_static/3/create.png)

* Ustaw metodę na GET.
* Wklej identyfikator URI (na przykład `https://localhost:5001/api/TodoItems/1`).
* Wybierz pozycję **Wyślij**.

## <a name="examine-the-get-methods"></a>Sprawdź metody GET

Te metody implementują dwa punkty końcowe GET:

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

Przetestuj aplikację, wywołując dwa punkty końcowe z przeglądarki lub listonosza. Przykład:

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

Odpowiedź podobna do następującej jest `GetTodoItems`wywoływana przez wezwanie do:

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a>Test Get z Listonoszem

* Utwórz nowe żądanie.
* Ustaw metodę HTTP na **GET**.
* Ustaw adres URL `https://localhost:<port>/api/TodoItems`żądania na . Na przykład `https://localhost:5001/api/TodoItems`.
* Ustaw **widok dwóch okienek** w postmanie.
* Wybierz pozycję **Wyślij**.

Ta aplikacja korzysta z bazy danych w pamięci. Jeśli aplikacja zostanie zatrzymana i uruchomiona, poprzednie żądanie GET nie zwróci żadnych danych. Jeśli żadne dane nie są zwracane, dane [POST](#post) do aplikacji.

## <a name="routing-and-url-paths"></a>Ścieżki routingu i adresów URL

Atrybut [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) oznacza metodę, która odpowiada na żądanie HTTP GET. Ścieżka adresu URL dla każdej metody jest skonstruowana w następujący sposób:

* Zacznij od ciągu szablonu w `Route` atrybucie kontrolera:

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* Zamień `[controller]` na nazwę kontrolera, który zgodnie z konwencją jest nazwą klasy kontrolera minus sufiks "Controller". W tym przykładzie nazwa klasy kontrolera to **TodoItems**Controller, więc nazwa kontrolera to "TodoItems". ASP.NET [Routingu rdzenia](xref:mvc/controllers/routing) jest niewrażliwe na wielkości liter.
* Jeśli `[HttpGet]` atrybut ma szablon trasy (na `[HttpGet("products")]`przykład ), dołącz go do ścieżki. W tym przykładzie nie jest używany szablon. Aby uzyskać więcej informacji, zobacz [Routing atrybutów z atrybutami Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).

W poniższej `GetTodoItem` `"{id}"` metodzie jest zmienną zastępczą dla unikatowego identyfikatora elementu do wykonania. Po `GetTodoItem` wywołaniu wartość `"{id}"` w adresie URL jest dostarczana `id` do metody w jego parametrze.

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a>Zwracane wartości

Typ zwracany `GetTodoItems` i `GetTodoItem` metody jest [ActionResult\<T> typu](xref:web-api/action-return-types#actionresultt-type). ASP.NET Core automatycznie serializuje obiekt do [JSON](https://www.json.org/) i zapisuje JSON w treści komunikatu odpowiedzi. Kod odpowiedzi dla tego typu zwracania wynosi 200, przy założeniu, że nie ma żadnych nieobsługiwał wyjątków. Nieobsługiwały się wyjątkami są tłumaczone na błędy 5xx.

`ActionResult`zwracane typy mogą reprezentować szeroki zakres kodów stanu HTTP. Na przykład `GetTodoItem` może zwrócić dwie różne wartości stanu:

* Jeśli żaden element nie pasuje do żądanego identyfikatora, metoda zwraca kod błędu [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) 404.
* W przeciwnym razie metoda zwraca 200 z treści odpowiedzi JSON. Zwracanie `item` wyników w odpowiedzi HTTP 200.

## <a name="the-puttodoitem-method"></a>Metoda PutTodoItem

Zbadaj `PutTodoItem` metodę:

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

`PutTodoItem`jest podobny `PostTodoItem`do , z tą różnicą, że używa HTTP PUT. Odpowiedź to [204 (Brak zawartości).](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) Zgodnie ze specyfikacją HTTP żądanie PUT wymaga od klienta wysłania całej zaktualizowanej jednostki, a nie tylko zmian. Aby obsługiwać częściowe aktualizacje, należy użyć protokołu [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).

Jeśli wystąpi błąd `PutTodoItem`wywołanie `GET` , wywołanie, aby upewnić się, że istnieje element w bazie danych.

### <a name="test-the-puttodoitem-method"></a>Przetestuj metodę PutTodoItem

W tym przykładzie użyto bazy danych w pamięci, która musi zostać zainicjowana przy każdym uruchomieniu aplikacji. Musi istnieć element w bazie danych przed dokonaniem wywołania PUT. Wywołanie GET, aby upewnić się, że jest element w bazie danych przed wykonaniem wywołania PUT.

Zaktualizuj element do wykonania o identyfikatorze = 1 i ustaw jego nazwę na "feed fish":

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

Na poniższej ilustracji przedstawiono aktualizację Listonosz:

![Konsola listonosza z odpowiedzią 204 (brak zawartości)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a>Metoda DeleteTodoItem

Zbadaj `DeleteTodoItem` metodę:

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a>Testowanie metody DeleteTodoItem

Użyj listonosza, aby usunąć element do wykonania:

* Ustaw metodę `DELETE`na .
* Ustaw identyfikator URI obiektu do usunięcia `https://localhost:5001/api/TodoItems/1`(na przykład ).
* Wybierz pozycję **Wyślij**.

<a name="over-post"></a>

## <a name="prevent-over-posting"></a>Zapobieganie nadmiernemu księgowaniu

Obecnie przykładowa aplikacja udostępnia `TodoItem` cały obiekt. Aplikacje Productions zazwyczaj ograniczają dane, które są wprowadzane do danych wejściowych i zwracane przy użyciu podzbioru modelu. Istnieje wiele powodów, dla których jest to ważne, a bezpieczeństwo jest najważniejsze. Podzbiór modelu jest zwykle określany jako obiekt transferu danych (DTO), model wejściowy lub model widoku. **DTO** jest używany w tym artykule.

DTO może być stosowany do:

* Zapobiegaj nadmiernemu księgowaniu.
* Ukryj właściwości, których klienci nie powinni wyświetlać.
* Pomiń niektóre właściwości w celu zmniejszenia rozmiaru ładunku.
* Spłaszcz wykresy obiektów zawierających obiekty zagnieżdżone. Wykresy spłaszczonych obiektów mogą być wygodniejsze dla klientów.

Aby zademonstrować podejście DTO, zaktualizuj klasę, `TodoItem` aby uwzględniła tajne pole:

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

Pole tajne musi być ukryte przed tą aplikacją, ale aplikacja administracyjna może go udostępnić.

Sprawdź, czy możesz zaksięgować i uzyskać tajne pole.

Utwórz model DTO:

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

Zaktualizuj `TodoItemsController` do użycia: `TodoItemDTO`

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

Sprawdź, czy nie możesz zaksięgować ani nie uzyskać pola tajnego.

## <a name="call-the-web-api-with-javascript"></a>Wywoływanie internetowego interfejsu API za pomocą języka JavaScript

Zobacz [Samouczek: Wywołanie ASP.NET core internet API z JavaScript](xref:tutorials/web-api-javascript).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie projektu internetowego interfejsu API.
> * Dodaj klasę modelu i kontekst bazy danych.
> * Dodaj kontroler.
> * Dodaj metody CRUD.
> * Konfigurowanie ścieżek routingu i adresów URL.
> * Określ wartości zwracane.
> * Wywołanie internetowego interfejsu API za pomocą listonosza.
> * Wywołanie internetowego interfejsu API za pomocą języka JavaScript.

Na końcu masz internetowy interfejs API, który może zarządzać elementami "do wykonania" przechowywanymi w relacyjnej bazie danych.

## <a name="overview"></a>Omówienie

W tym samouczku utworzy się następujący interfejs API:

|interfejs API | Opis | Treść żądania | Treść odpowiedzi |
|--- | ---- | ---- | ---- |
|POBIERZ /api/TodoItems | Pobierz wszystkie przedmioty do zrobienia | Brak | Tablica elementów do wykonania|
|POBIERZ /api/TodoItems/{id} | Uzyskaj przedmiot według identyfikatora | Brak | Element do wykonania|
|POST /api/TodoItems | Dodawanie nowego elementu | Element do wykonania | Element do wykonania |
|PUT /api/TodoItems/{id} | Aktualizowanie istniejącego elementu&nbsp; | Element do wykonania | Brak |
|USUŃ /api/TodoItems/{id} &nbsp;&nbsp; | Usuwanie elementu &nbsp;&nbsp; | Brak | Brak|

Na poniższym diagramie przedstawiono projekt aplikacji.

![Klient jest reprezentowany przez pole po lewej stronie. Składa wniosek i otrzymuje odpowiedź od wniosku, pole sporządzone po prawej stronie. W polu aplikacji trzy pola reprezentują kontroler, model i warstwę dostępu do danych. Żądanie jest do kontrolera aplikacji i operacje odczytu/zapisu występują między kontrolerem a warstwą dostępu do danych. Model jest serializowany i zwracany do klienta w odpowiedzi.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a>Tworzenie projektu sieci Web

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Z menu **Plik** wybierz polecenie **Nowy** > **projekt**.
* Wybierz szablon **ASP.NET Core Web Application** i kliknij przycisk **Dalej**.
* Nazwij projekt *TodoApi* i kliknij przycisk **Utwórz**.
* W oknie **dialogowym Tworzenie nowej ASP.NET core web application** upewnij się, że wybrano opcję **.NET Core** i ASP.NET Core **2.2.** Wybierz szablon **interfejsu API** i kliknij przycisk **Utwórz**. **Nie wybieraj** opcji **Włącz obsługę platformy Docker**.

![Vs nowe okno dialogowe projektu](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Zmień katalogi`cd`( ) na folder, który będzie zawierał folder projektu.
* Uruchom następujące polecenia:

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  Te polecenia tworzą nowy projekt interfejsu API sieci web i otwierają nowe wystąpienie programu Visual Studio Code w nowym folderze projektu.

* Gdy w oknie dialogowym pojawi się pytanie, czy chcesz dodać wymagane zasoby do projektu, wybierz opcję **Tak**.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* Wybierz **pozycję Plik** > **nowego rozwiązania**.

  ![macOS Nowe rozwiązanie](first-web-api-mac/_static/sln.png)

* Wybierz następnie **pozycję .NET Core** > **App** > **API** > **.**

  ![macOS Nowe okno dialogowe projektu](first-web-api-mac/_static/1.png)
  
* W oknie dialogowym **Konfigurowanie nowego interfejsu API sieci Web ASP.NET Core** zaakceptuj domyślną **ramę docelową** **.NET Core 2.2*.

* Wprowadź *pozycję TodoApi* dla **nazwy projektu,** a następnie wybierz pozycję **Utwórz**.

  ![okno dialogowe konfiguracji](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a>Testowanie interfejsu API

Szablon projektu tworzy `values` interfejs API. Wywołanie `Get` metody z przeglądarki, aby przetestować aplikację.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Naciśnij klawisze Ctrl+F5, aby uruchomić aplikację. Visual Studio uruchamia przeglądarkę `https://localhost:<port>/api/values`i `<port>` przechodzi do , gdzie jest losowo wybrany numer portu.

Jeśli pojawi się okno dialogowe z pytaniem, czy certyfikat IIS Express ma być zaufany, wybierz opcję **Tak**. W oknie dialogowym **Ostrzeżenie o zabezpieczeniach,** które pojawi się obok, wybierz pozycję **Tak**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Naciśnij klawisze Ctrl+F5, aby uruchomić aplikację. W przeglądarce przejdź do `https://localhost:5001/api/values`następującego adresu URL: .

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

Wybierz **uruchom** > **debugowanie startowe,** aby uruchomić aplikację. Program Visual Studio dla komputerów `https://localhost:<port>`Mac `<port>` uruchamia przeglądarkę i przechodzi do miejsca, w którym jest losowo wybrany numer portu. Zwracany jest błąd HTTP 404 (nie znaleziono). Dołącz `/api/values` do adresu URL (zmień `https://localhost:<port>/api/values`adres URL na ).

---

Zostanie zwrócony następujący kod JSON:

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a>Dodawanie klasy modelu

*Model* to zestaw klas, które reprezentują dane, którymi zarządza aplikacja. Model dla tej aplikacji `TodoItem` jest pojedynczą klasą.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* W **Eksploratorze rozwiązań**kliknij projekt prawym przyciskiem myszy. Wybierz **pozycję Dodaj** > **nowy folder**. Nazwij folder *Models*.

* Kliknij prawym przyciskiem myszy folder *Modele* i wybierz polecenie **Dodaj** > **klasę**. Nazwij klasę *TodoItem* i wybierz pozycję **Dodaj**.

* Zastąp kod szablonu następującym kodem:

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Dodawanie folderu o nazwie *Modele*.

* Dodaj `TodoItem` klasę do folderu *Models* z następującym kodem:

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* Kliknij prawym przyciskiem myszy projekt. Wybierz **pozycję Dodaj** > **nowy folder**. Nazwij folder *Models*.

  ![nowy folder](first-web-api-mac/_static/folder.png)

* Kliknij prawym przyciskiem myszy folder *Modele* i wybierz polecenie **Dodaj** > **nową** > **klasę opróżnienia ogólnego** > **Empty Class**pliku .

* Nazwij klasę *TodoItem*, a następnie kliknij przycisk **Nowy**.

* Zastąp kod szablonu następującym kodem:

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

Właściwość `Id` działa jako unikatowy klucz w relacyjnej bazie danych.

Klasy modelu można przejść w dowolnym miejscu w projekcie, ale *Models* folder jest używany przez konwencję.

## <a name="add-a-database-context"></a>Dodawanie kontekstu bazy danych

*Kontekst bazy danych* jest klasą główną, która koordynuje funkcjonalność entity framework dla modelu danych. Ta klasa jest tworzona `Microsoft.EntityFrameworkCore.DbContext` przez wyprowadzanie z klasy.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Kliknij prawym przyciskiem myszy folder *Modele* i wybierz polecenie **Dodaj** > **klasę**. Nazwij klasę *TodoContext* i kliknij przycisk **Dodaj**.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Kod programu Visual Studio / Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

* Dodaj `TodoContext` klasę do folderu *Modele.*

---

* Zastąp kod szablonu następującym kodem:

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a>Rejestrowanie kontekstu bazy danych

W ASP.NET Core usługi, takie jak kontekst bazy danych, muszą być zarejestrowane w kontenerze [iniekcji zależności (DI).](xref:fundamentals/dependency-injection) Kontener zapewnia usługę do kontrolerów.

Zaktualizuj *Startup.cs* następującym wyróżnionym kodem:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

Powyższy kod ma następujące działanie:

* Usuwa nieużywane `using` deklaracje.
* Dodaje kontekst bazy danych do kontenera DI.
* Określa, że kontekst bazy danych będzie używany w bazie danych w pamięci.

## <a name="add-a-controller"></a>Dodawanie kontrolera

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Kliknij prawym przyciskiem myszy folder *Kontrolery.*
* Wybierz **pozycję Dodaj** > **nowy element**.
* W oknie dialogowym **Dodawanie nowego elementu** wybierz szablon klasy **kontrolera interfejsu API.**
* Nazwij klasę *TodoController*i wybierz pozycję **Dodaj**.

  ![Dodawanie nowego okna dialogowego Element z kontrolerem w polu wyszukiwania i wybranym kontrolerem interfejsu API sieci Web](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Kod programu Visual Studio / Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

* W folderze *Kontrolery* utwórz `TodoController`klasę o nazwie .

---

* Zastąp kod szablonu następującym kodem:

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

Powyższy kod ma następujące działanie:

* Definiuje klasę kontrolera interfejsu API bez metod.
* Oznacza klasę atrybutem. [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) Ten atrybut wskazuje, że kontroler odpowiada na żądania interfejsu API sieci web. Aby uzyskać informacje o określonych zachowaniach, <xref:web-api/index>które umożliwia atrybut, zobacz .
* Używa DI, aby wstrzyknąć kontekst bazy danych (`TodoContext`) do kontrolera. Kontekst bazy danych jest używany w każdej z metod [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) w kontrolerze.
* Dodaje element `Item1` o nazwie do bazy danych, jeśli baza danych jest pusta. Ten kod znajduje się w konstruktorze, więc jest uruchamiany za każdym razem, gdy jest nowe żądanie HTTP. Jeśli usuniesz wszystkie elementy, konstruktor tworzy `Item1` ponownie następnym razem, gdy wywoływana jest metoda interfejsu API. Więc może to wyglądać usunięcie nie działa, gdy rzeczywiście nie działa.

## <a name="add-get-methods"></a>Dodaj metody Pobierz

Aby zapewnić interfejs API, który pobiera elementy do wykonania, dodaj do `TodoController` klasy następujące metody:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

Te metody implementują dwa punkty końcowe GET:

* `GET /api/todo`
* `GET /api/todo/{id}`

Zatrzymaj aplikację, jeśli jest nadal uruchomiona. Następnie uruchom go ponownie, aby uwzględnić najnowsze zmiany.

Przetestuj aplikację, wywołując dwa punkty końcowe z przeglądarki. Przykład:

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

Następująca odpowiedź HTTP jest wywoływana przez wywołanie: `GetTodoItems`

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a>Ścieżki routingu i adresów URL

Atrybut [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) oznacza metodę, która odpowiada na żądanie HTTP GET. Ścieżka adresu URL dla każdej metody jest skonstruowana w następujący sposób:

* Zacznij od ciągu szablonu w `Route` atrybucie kontrolera:

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* Zamień `[controller]` na nazwę kontrolera, który zgodnie z konwencją jest nazwą klasy kontrolera minus sufiks "Controller". W tym przykładzie nazwa klasy kontrolera to **Kontroler Dodo,** więc nazwa kontrolera jest "todo". ASP.NET [Routingu rdzenia](xref:mvc/controllers/routing) jest niewrażliwe na wielkości liter.
* Jeśli `[HttpGet]` atrybut ma szablon trasy (na `[HttpGet("products")]`przykład ), dołącz go do ścieżki. W tym przykładzie nie jest używany szablon. Aby uzyskać więcej informacji, zobacz [Routing atrybutów z atrybutami Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).

W poniższej `GetTodoItem` `"{id}"` metodzie jest zmienną zastępczą dla unikatowego identyfikatora elementu do wykonania. Po `GetTodoItem` wywołaniu wartość `"{id}"` w adresie URL jest dostarczana`id` do metody w jego parametrze.

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a>Zwracane wartości

Typ zwracany `GetTodoItems` i `GetTodoItem` metody jest [ActionResult\<T> typu](xref:web-api/action-return-types#actionresultt-type). ASP.NET Core automatycznie serializuje obiekt do [JSON](https://www.json.org/) i zapisuje JSON w treści komunikatu odpowiedzi. Kod odpowiedzi dla tego typu zwracania wynosi 200, przy założeniu, że nie ma żadnych nieobsługiwał wyjątków. Nieobsługiwały się wyjątkami są tłumaczone na błędy 5xx.

`ActionResult`zwracane typy mogą reprezentować szeroki zakres kodów stanu HTTP. Na przykład `GetTodoItem` może zwrócić dwie różne wartości stanu:

* Jeśli żaden element nie pasuje do żądanego identyfikatora, metoda zwraca kod błędu [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) 404.
* W przeciwnym razie metoda zwraca 200 z treści odpowiedzi JSON. Zwracanie `item` wyników w odpowiedzi HTTP 200.

## <a name="test-the-gettodoitems-method"></a>Przetestuj metodę GetTodoItems

Ten samouczek używa postmana do testowania internetowego interfejsu API.

* Zainstaluj [Listonosz](https://www.getpostman.com/downloads/).
* Uruchom aplikację internetową.
* Rozpocznij postman.
* Wyłącz **weryfikację certyfikatu SSL**.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* W **obszarze** > **Ustawienia** plików (karta**Ogólne)** wyłącz **weryfikację certyfikatu SSL**.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Kod programu Visual Studio / Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

* Z **preferencji listonosza** > **Preferences** (karta**Ogólne)** wyłącz **weryfikację certyfikatu SSL**. Możesz też wybrać klucz i wybrać **ustawienia**, a następnie wyłączyć weryfikację certyfikatu SSL.

---
  
> [!WARNING]
> Ponownie włącz weryfikację certyfikatu SSL po przetestowaniu sterownika.

* Utwórz nowe żądanie.
  * Ustaw metodę HTTP na **GET**.
  * Ustaw adres URL `https://localhost:<port>/api/todo`żądania na . Na przykład `https://localhost:5001/api/todo`.
* Ustaw **widok dwóch okienek** w postmanie.
* Wybierz pozycję **Wyślij**.

![Listonosz z prośbą o uzyskanie](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a>Dodawanie metody tworzenia

Dodaj następującą `PostTodoItem` metodę wewnątrz *controllers/TodoController.cs*: 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

Poprzedni kod jest http post metody, zgodnie [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) z atrybutem. Metoda pobiera wartość elementu do wykonania z treści żądania HTTP.

Metoda: `CreatedAtAction`

* Zwraca kod stanu HTTP 201, jeśli zakończy się pomyślnie. HTTP 201 jest standardową odpowiedzią dla metody HTTP POST, która tworzy nowy zasób na serwerze.
* Dodaje `Location` nagłówek do odpowiedzi. Nagłówek `Location` określa identyfikator URI nowo utworzonego elementu do wykonania. Aby uzyskać więcej informacji, zobacz [10.2.2 201 Utworzono](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).
* Odwołuje się `GetTodoItem` do akcji, aby utworzyć `Location` identyfikator URI nagłówka. C# `nameof` słowo kluczowe jest używany w celu uniknięcia `CreatedAtAction` twardego kodowania nazwę akcji w wywołaniu.

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a>Przetestuj metodę PostTodoItem

* Skompiluj projekt.
* W u wysłać metodę `POST`Http na .
* Wybierz kartę **Treść**.
* Wybierz **nieprzetworzony** przycisk radiowy.
* Ustaw typ na **JSON (application/json)**.
* W treści żądania wprowadź JSON dla elementu do wykonania:

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* Wybierz pozycję **Wyślij**.

  ![Listonosz z żądaniem utworzenia](first-web-api/_static/create.png)

  Jeśli otrzymasz błąd 405 Metoda niedozwolone, prawdopodobnie jest wynikiem nie kompilacji projektu po dodaniu `PostTodoItem` metody.

### <a name="test-the-location-header-uri"></a>Testowanie identyfikatora URI nagłówka lokalizacji

* Wybierz kartę **Nagłówki** w okienku **Odpowiedzi.**
* Skopiuj wartość **nagłówka Lokalizacja:**

  ![Karta Nagłówki konsoli Listonosz](first-web-api/_static/pmc2.png)

* Ustaw metodę na GET.
* Wklej identyfikator URI (na przykład `https://localhost:5001/api/Todo/2`).
* Wybierz pozycję **Wyślij**.

## <a name="add-a-puttodoitem-method"></a>Dodawanie metody PutTodoItem

Dodaj następującą `PutTodoItem` metodę:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

`PutTodoItem`jest podobny `PostTodoItem`do , z tą różnicą, że używa HTTP PUT. Odpowiedź to [204 (Brak zawartości).](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) Zgodnie ze specyfikacją HTTP żądanie PUT wymaga od klienta wysłania całej zaktualizowanej jednostki, a nie tylko zmian. Aby obsługiwać częściowe aktualizacje, należy użyć protokołu [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).

Jeśli wystąpi błąd `PutTodoItem`wywołanie `GET` , wywołanie, aby upewnić się, że istnieje element w bazie danych.

### <a name="test-the-puttodoitem-method"></a>Przetestuj metodę PutTodoItem

W tym przykładzie użyto bazy danych w pamięci, która musi zostać zainicjowana przy każdym uruchomieniu aplikacji. Musi istnieć element w bazie danych przed dokonaniem wywołania PUT. Wywołanie GET, aby upewnić się, że jest element w bazie danych przed wykonaniem wywołania PUT.

Zaktualizuj element do wykonania, który ma identyfikator = 1 i ustaw jego nazwę na "feed fish":

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

Na poniższej ilustracji przedstawiono aktualizację Listonosz:

![Konsola listonosza z odpowiedzią 204 (brak zawartości)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a>Dodawanie metody DeleteTodoItem

Dodaj następującą `DeleteTodoItem` metodę:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

Odpowiedź `DeleteTodoItem` to [204 (Brak zawartości).](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)

### <a name="test-the-deletetodoitem-method"></a>Testowanie metody DeleteTodoItem

Użyj listonosza, aby usunąć element do wykonania:

* Ustaw metodę `DELETE`na .
* Ustaw identyfikator URI obiektu do usunięcia `https://localhost:5001/api/todo/1`(na przykład ).
* Wybierz pozycję **Wyślij**.

Przykładowa aplikacja umożliwia usunięcie wszystkich elementów. Jednak po usunięciu ostatniego elementu, nowy jest tworzony przez konstruktora klasy modelu przy następnym wywołaniu interfejsu API.

## <a name="call-the-web-api-with-javascript"></a>Wywoływanie internetowego interfejsu API za pomocą języka JavaScript

W tej sekcji zostanie dodana strona HTML, która używa języka JavaScript do wywoływania internetowego interfejsu API. jQuery inicjuje żądanie. JavaScript aktualizuje stronę ze szczegółami z odpowiedzi internetowego interfejsu API.

Skonfiguruj aplikację do [obsługi plików statycznych](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) i [włącz domyślne mapowanie plików,](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) aktualizując *Startup.cs* za pomocą następującego podświetlanych kodów:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

Utwórz folder *wwwroot* w katalogu projektu.

Dodaj plik HTML o nazwie *index.html* do katalogu *wwwroot.* Zastąp jego zawartość następującym znacznikiem:

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

Dodaj plik JavaScript o nazwie *site.js* do katalogu *wwwroot.* Zastąp jego zawartość następującym kodem:

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

Aby przetestować stronę HTML lokalnie, może być wymagana zmiana ustawień uruchamiania projektu ASP.NET Core:

* Otwórz *właściwości\launchSettings.json*.
* Usuń `launchUrl` właściwość, aby wymusić otwarcie aplikacji w *pliku index.html*&mdash;domyślnego pliku projektu.

W tym przykładzie wywołuje wszystkie metody CRUD interfejsu API sieci web. Poniżej przedstawiono objaśnienia wywołań interfejsu API.

### <a name="get-a-list-of-to-do-items"></a>Wyświetlanie listy elementów do wykonania

jQuery wysyła żądanie HTTP GET do internetowego interfejsu API, który zwraca JSON reprezentujący tablicę elementów do wykonania. Funkcja `success` wywołania zwrotnego jest wywoływana, jeśli żądanie zakończy się pomyślnie. W wywołaniu zwrotnym dom jest aktualizowany o informacje do wykonania.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a>Dodawanie elementu do wykonania

jQuery wysyła żądanie HTTP POST z elementem do wykonania w treści żądania. Opcje `accepts` `contentType` i opcje `application/json` są ustawione tak, aby określić typ nośnika odbierany i wysyłany. Element do wykonania jest konwertowany na JSON przy użyciu pliku [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify). Gdy interfejs API zwraca kod `getData` stanu pomyślnego, funkcja jest wywoływana w celu zaktualizowania tabeli HTML.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a>Aktualizowanie elementu do wykonania

Aktualizowanie elementu do wykonania jest podobne do dodawania jednego. Zmiany, `url` aby dodać unikatowy identyfikator elementu, `type` `PUT`a jest .

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a>Usuwanie elementu do wykonania

Usunięcie elementu do wykonania odbywa się poprzez `type` ustawienie wywołania `DELETE` AJAX i określenie unikatowego identyfikatora elementu w adresie URL.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a>Dodawanie obsługi uwierzytelniania do internetowego interfejsu API

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a>Zasoby dodatkowe

[Wyświetl lub pobierz przykładowy kod dla tego samouczka](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples). Zobacz, [jak pobrać](xref:index#how-to-download-a-sample)plik .

Więcej informacji zawierają następujące zasoby:

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [Wersja tego samouczka w YouTube](https://www.youtube.com/watch?v=TTkhEyGBfAk)
