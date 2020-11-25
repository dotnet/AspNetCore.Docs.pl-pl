---
title: 'Samouczek: Tworzenie internetowego interfejsu API za pomocą ASP.NET Core'
author: rick-anderson
description: Dowiedz się, jak utworzyć internetowy interfejs API za pomocą ASP.NET Core.
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 08/13/2020
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
- Models
uid: tutorials/first-web-api
ms.openlocfilehash: 9299ba685c95ced522fc725854a66252e67fc799
ms.sourcegitcommit: aa85f2911792a1e4783bcabf0da3b3e7e218f63a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/23/2020
ms.locfileid: "96024989"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a>Samouczek: Tworzenie internetowego interfejsu API za pomocą ASP.NET Core

Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirka Larkin](https://twitter.com/serpent5)i [Jan Wasson](https://github.com/mikewasson)

Ten samouczek uczy się podstaw tworzenia interfejsu API sieci Web za pomocą ASP.NET Core.

::: moniker range=">= aspnetcore-5.0"

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Utwórz projekt interfejsu API sieci Web.
> * Dodaj klasę modelu i kontekst bazy danych.
> * Tworzy szkielet kontrolera z metodami CRUD.
> * Skonfiguruj Routing, ścieżki URL i wartości zwracane.
> * Wywołaj interfejs API sieci Web za pomocą programu Poster.

Na końcu znajduje się internetowy interfejs API, który może zarządzać elementami do wykonania przechowywanymi w bazie danych.

## <a name="overview"></a>Omówienie

Ten samouczek tworzy następujący interfejs API:

|Interfejs API | Opis | Treść żądania | Treść odpowiedzi |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | Pobierz wszystkie elementy do wykonania | Brak | Tablica elementów do wykonania|
|`GET /api/TodoItems/{id}` | Pobieranie elementu według identyfikatora | Brak | Element do wykonania|
|`POST /api/TodoItems` | Dodaj nowy element | Element do wykonania | Element do wykonania |
|`PUT /api/TodoItems/{id}` | Aktualizowanie istniejącego elementu &nbsp; | Element do wykonania | Brak |
|`DELETE /api/TodoItems/{id}` &nbsp; &nbsp; | Usuń element &nbsp;&nbsp; | Brak | Brak|

Na poniższym diagramie przedstawiono projekt aplikacji.

![Klient jest reprezentowany przez pole po lewej stronie. Przesyła żądanie i odbiera odpowiedź z aplikacji, pole rysowane po prawej stronie. W polu aplikacja trzy pola reprezentują kontroler, model i warstwę dostępu do danych. Żądanie znajduje się w kontrolerze aplikacji, a operacje odczytu i zapisu są wykonywane między kontrolerem a warstwą dostępu do danych. Model jest serializowany i zwracany do klienta w odpowiedzi.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-project"></a>Tworzenie projektu sieci Web

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Z menu **plik** wybierz pozycję **Nowy** > **projekt**.
* Wybierz szablon **aplikacja sieci Web ASP.NET Core** a następnie kliknij przycisk **dalej**.
* Nazwij projekt *TodoApi* i kliknij pozycję **Utwórz**.
* W oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** upewnij się, że wybrano opcję **.net Core** i **ASP.NET Core 5,0** . Wybierz szablon **interfejsu API** i kliknij przycisk **Utwórz**.

![Okno dialogowe programu VS New Project](first-web-api/_static/5/vs.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Zmień katalog ( `cd` ) do folderu, który będzie zawierać folder projektu.
* Uruchom następujące polecenia:

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* Gdy zostanie wyświetlone okno dialogowe z pytaniem, czy chcesz dodać wymagane zasoby do projektu, wybierz opcję **tak**.

  Poprzednie polecenia:

  * Tworzy nowy projekt internetowego interfejsu API i otwiera go w Visual Studio Code.
  * Dodaje pakiety NuGet, które są wymagane w następnej sekcji.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* Wybierz pozycję **plik** > **nowe rozwiązanie**.

  ![macOS nowe rozwiązanie](first-web-api-mac/_static/sln.png)

* W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz **.NET Core** pozycję  >  **App**  >  **interfejs API** aplikacji .NET Core  >  **Next**. W wersji 8,6 lub nowszej wybierz pozycję **Web and Console**  >  **App**  >  **interfejs API**  >  **Next** aplikacji sieci Web i konsoli.

  ![Wybór szablonu interfejsu API macOS](first-web-api-mac/_static/api_template.png)

* W oknie dialogowym **Konfigurowanie nowego interfejsu API sieci Web ASP.NET Core** wybierz najnowszą platformę **docelową**.NET Core 5. x. Wybierz pozycję **Dalej**.

* Wprowadź *TodoApi* jako **nazwę projektu** , a następnie wybierz pozycję **Utwórz**.

  ![okno dialogowe konfiguracji](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

Otwórz Terminal poleceń w folderze projektu i uruchom następujące polecenia:

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-project"></a>Testowanie projektu

Szablon projektu tworzy `WeatherForecast` interfejs API z obsługą [struktury Swagger](xref:tutorials/web-api-help-pages-using-swagger).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Naciśnij klawisze CTRL + F5, aby uruchomić bez debugera.

[!INCLUDE[](~/includes/trustCertVS.md)]

  Zostanie uruchomiony program Visual Studio:

* Serwer sieci Web IIS Express.
* Domyślna przeglądarka i nawiguje do `https://localhost:<port>/swagger/index.html` , gdzie `<port>` jest losowo wybranym numerem portu.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/trustCertVSC.md)]

Naciśnij klawisze CTRL + F5, aby uruchomić aplikację. W przeglądarce przejdź do następującego adresu URL: [https://localhost:5001/swagger](https://localhost:5001/swagger)

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

Wybierz pozycję **Uruchom**  >  **Rozpocznij debugowanie** , aby uruchomić aplikację. Visual Studio dla komputerów Mac uruchamia przeglądarkę i nawiguje do `https://localhost:<port>` , gdzie `<port>` jest losowo wybranym numerem portu. Zwracany jest błąd HTTP 404 (nie znaleziono). Dołącz `/swagger` do adresu URL (Zmień adres URL na `https://localhost:<port>/swagger` ).

---

`/swagger/index.html`Zostanie wyświetlona strona Swagger. Wybierz pozycję **Pobierz**  >  **spróbuj**  >  **wykonać** operację. Zostanie wyświetlona strona:

* [Zwinięcie](https://curl.haxx.se/) polecenie do testowania interfejsu API WeatherForecast.
* Adres URL służący do testowania interfejsu API WeatherForecast.
* Kod odpowiedzi, treść i nagłówki.
* Pole listy rozwijanej z typami nośników oraz przykładową wartością i schematem.

<!-- Review: Do we care the IE generates several errors. It shows the data, but with  Unrecognized response type; displaying content as text.
-->
Struktura Swagger służy do generowania przydatnej dokumentacji i stron pomocy dla interfejsów API sieci Web. Ten samouczek koncentruje się na tworzeniu interfejsu API sieci Web. Aby uzyskać więcej informacji na temat struktury Swagger, zobacz <xref:tutorials/web-api-help-pages-using-swagger> .

Skopiuj i wklej **adres URL żądania** w przeglądarce:  `https://localhost:<port>/WeatherForecast`

Zwracany jest kod JSON podobny do następującego:

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

### <a name="update-the-launchurl"></a>Aktualizowanie launchUrl

W *Properties\launchSettings.json* Update `launchUrl` od `"swagger"` do `"api/TodoItems"` :

```json
"launchUrl": "api/TodoItems",
```

Ze względu na to, że program Swagger został usunięty, poprzedzający znacznik zmienia adres URL, który jest uruchamiany w metodzie GET kontrolera dodanej w poniższych sekcjach.

## <a name="add-a-model-class"></a>Dodaj klasę modelu

*Model* to zestaw klas, które reprezentują dane zarządzane przez aplikację. Model tej aplikacji jest pojedynczą `TodoItem` klasą.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt. Wybierz pozycję **Dodaj**  >  **Nowy folder**. Nazwij folder *Models* .

* Kliknij prawym przyciskiem myszy *Models* folder i wybierz polecenie **Dodaj**  >  **klasę**. Nadaj klasie nazwę *TodoItem* i wybierz pozycję **Dodaj**.

* Zastąp kod szablonu następującym:

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Dodaj folder o nazwie *Models* .

* Dodaj `TodoItem` do *Models* folderu klasę o następującym kodzie:

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* Kliknij prawym przyciskiem myszy projekt. Wybierz pozycję **Dodaj**  >  **Nowy folder**. Nazwij folder *Models* .

  ![Nowy folder](first-web-api-mac/_static/folder.png)

* Kliknij prawym przyciskiem myszy *Models* folder, a następnie wybierz pozycję **Dodaj** > **nowy plik** > **ogólna** > **pusta Klasa**.

* Nazwij klasę *TodoItem*, a następnie kliknij pozycję **New (nowy**).

* Zastąp kod szablonu następującym:

---

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoItem.cs?name=snippet)]

`Id`Właściwość działa jako unikatowy klucz w relacyjnej bazie danych.

Klasy modelu mogą przejść do dowolnego miejsca w projekcie, ale *Models* folder jest używany przez Konwencję.

## <a name="add-a-database-context"></a>Dodawanie kontekstu bazy danych

*Kontekst bazy danych* jest główną klasą, która koordynuje Entity Framework funkcji dla modelu danych. Ta klasa jest tworzona przez wyprowadzanie z <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> klasy.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="add-nuget-packages"></a>Dodawanie pakietów NuGet

* W menu **Narzędzia** wybierz pozycję **menedżer pakietów NuGet > zarządzanie pakietami NuGet dla rozwiązania**.
* Wybierz kartę **Przeglądaj** , a następnie w polu wyszukiwania wprowadź ciąg **Microsoft. EntityFrameworkCore. SqlServer** .
<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Delete this line at RTM -->
* W lewym okienku wybierz pozycję **Microsoft. EntityFrameworkCore. SqlServer** .
* Zaznacz pole wyboru **projekt** w prawym okienku, a następnie wybierz pozycję **Zainstaluj**.
* Użyj powyższych instrukcji, aby dodać pakiet NuGet **Microsoft. EntityFrameworkCore. inMemory** .

<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Update this image at RTM -->
![Menedżer pakietów NuGet](first-web-api/_static/5/vsNuGet.png)

## <a name="add-the-todocontext-database-context"></a>Dodawanie kontekstu bazy danych TodoContext

* Kliknij prawym przyciskiem myszy *Models* folder i wybierz polecenie **Dodaj**  >  **klasę**. Nadaj klasie nazwę *TodoContext* i kliknij przycisk **Dodaj**.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

* Dodaj `TodoContext` klasę do *Models* folderu.

---

* Wprowadź następujący kod:

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a>Rejestrowanie kontekstu bazy danych

W ASP.NET Core usługi, takie jak kontekst bazy danych, muszą być zarejestrowane z kontenerem [iniekcji zależności (di)](xref:fundamentals/dependency-injection) . Kontener udostępnia usługę kontrolerom.

Zaktualizuj *Startup.cs* przy użyciu następującego kodu:

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

Powyższy kod ma następujące działanie:

* Usuwa wywołania struktury Swagger.
* Usuwa nieużywane `using` deklaracje.
* Dodaje kontekst bazy danych do kontenera DI.
* Określa, że kontekst bazy danych będzie używać bazy danych w pamięci.

## <a name="scaffold-a-controller"></a>Tworzenie szkieletu kontrolera

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Kliknij prawym przyciskiem myszy folder *controllers* .
* Wybierz pozycję **Dodaj** > **nowy element szkieletowy**.
* Wybierz pozycję **kontroler interfejsu API z akcjami, używając Entity Framework**, a następnie wybierz pozycję **Dodaj**.
* Na stronie **Dodawanie kontrolera interfejsu API z akcjami przy użyciu Entity Framework** dialogowego:

  * Wybierz pozycję **TodoItem (TodoApi. Models )** w **klasie model**.
  * Wybierz pozycję **TodoContext (TodoApi. Models )** w **klasie kontekstu danych**.
  * Wybierz pozycję **Dodaj**.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

Uruchom następujące polecenia:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet tool update -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

Poprzednie polecenia:

* Dodaj pakiety NuGet wymagane do tworzenia szkieletów.
* Instaluje aparat tworzenia szkieletu ( `dotnet-aspnet-codegenerator` ).
* Szkielety `TodoItemsController` .

---

Wygenerowany kod:

* Oznacza klasę [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atrybutem. Ten atrybut wskazuje, że kontroler odpowiada na żądania interfejsu API sieci Web. Aby uzyskać informacje o określonych zachowaniach, które włącza atrybut, zobacz <xref:web-api/index> .
* Używa funkcji DI do iniekcji kontekstu bazy danych ( `TodoContext` ) do kontrolera. Kontekst bazy danych jest używany w każdej z metod [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) w kontrolerze.

Szablony ASP.NET Core dla:

* Kontrolery z widokami obejmują `[action]` szablon trasy.
* Kontrolery interfejsu API nie należą `[action]` do szablonu trasy.

Gdy `[action]` token nie znajduje się w szablonie trasy, nazwa [akcji](xref:mvc/controllers/routing#action) jest wykluczona z trasy. Oznacza to, że nazwa metody skojarzonej z akcją nie jest używana w zgodnej trasie.

## <a name="update-the-posttodoitem-create-method"></a>Aktualizowanie metody PostTodoItem Create

Zastąp instrukcję return w, `PostTodoItem` Aby użyć operatora [nameof](/dotnet/csharp/language-reference/operators/nameof) :

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

Poprzedni kod jest metodą POST protokołu HTTP, jak wskazano w [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) atrybucie. Metoda pobiera wartość elementu do wykonania z treści żądania HTTP.

Aby uzyskać więcej informacji, zobacz temat [Routing atrybutów z atrybutami http [Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).

<xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>Metoda:

* W razie powodzenia zwraca [kod stanu HTTP 201](https://developer.mozilla.org/docs/Web/HTTP/Status/201) . HTTP 201 to standardowa odpowiedź dla metody POST protokołu HTTP, która tworzy nowy zasób na serwerze.
* Dodaje nagłówek [lokalizacji](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) do odpowiedzi. `Location`Nagłówek określa [Identyfikator URI](https://developer.mozilla.org/docs/Glossary/URI) nowo utworzonego elementu do wykonania. Aby uzyskać więcej informacji, zobacz [10.2.2 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).
* Odwołuje się do `GetTodoItem` akcji tworzenia `Location` identyfikatora URI nagłówka. `nameof`Słowo kluczowe języka C# służy do zapobiegania twardemu kodowaniu nazwy akcji w `CreatedAtAction` wywołaniu.

### <a name="install-postman"></a>Zainstaluj program Poster

Ten samouczek używa programu do testowania interfejsu API sieci Web.

* Zainstaluj program [Poster](https://www.getpostman.com/downloads/)
* Uruchom aplikację internetową.
* Uruchom wpis.
* Wyłącz **weryfikację certyfikatu SSL**
  * W **File** obszarze > **Ustawienia** pliku (karta **Ogólne** ) Wyłącz **weryfikację certyfikatu SSL**.
    > [!WARNING]
    > Po przetestowaniu kontrolera ponownie Włącz weryfikację certyfikatu SSL.

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a>Test PostTodoItem za pomocą programu Poster

* Utwórz nowe żądanie.
* Ustaw metodę HTTP na `POST` .
* Ustaw identyfikator URI na `https://localhost:<port>/api/TodoItems` . Na przykład `https://localhost:5001/api/TodoItems`.
* Wybierz kartę **Treść**.
* Wybierz przycisk radiowy **RAW** .
* Ustaw typ na **JSON (Application/JSON)**.
* W treści żądania wprowadź kod JSON dla elementu do wykonania:

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* Wybierz pozycję **Wyślij**.

  ![Ogłoś przy użyciu żądania Create](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a>Testowanie identyfikatora URI nagłówka lokalizacji

Identyfikator URI nagłówka lokalizacji może zostać przetestowany w przeglądarce. Skopiuj i wklej identyfikator URI nagłówka lokalizacji do przeglądarki.

Aby przetestować w programie Poster:

* Wybierz kartę **nagłówki** w okienku **odpowiedź** .
* Skopiuj wartość nagłówka **lokalizacji** :

  ![Karta nagłówki w konsoli programu Poster](first-web-api/_static/3/create.png)

* Ustaw metodę HTTP na `GET` .
* Ustaw identyfikator URI na `https://localhost:<port>/api/TodoItems/1` . Na przykład `https://localhost:5001/api/TodoItems/1`.
* Wybierz pozycję **Wyślij**.

## <a name="examine-the-get-methods"></a>Badanie metod GET

Zaimplementowane są dwa punkty końcowe GET:

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

Przetestuj aplikację, wywołując dwa punkty końcowe z przeglądarki lub wpisu. Na przykład:

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

Odpowiedź podobna do poniższego jest generowana przez wywołanie `GetTodoItems` :

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a>Test get przy użyciu programu Poster

* Utwórz nowe żądanie.
* Ustaw metodę HTTP, aby **uzyskać**.
* Ustaw identyfikator URI żądania na `https://localhost:<port>/api/TodoItems` . Na przykład `https://localhost:5001/api/TodoItems`.
* Ustaw **dwa widoki okienka** w programie Poster.
* Wybierz pozycję **Wyślij**.

Ta aplikacja używa bazy danych w pamięci. Jeśli aplikacja zostanie zatrzymana i uruchomiona, poprzednie żądanie GET nie zwróci żadnych danych. Jeśli nie zostaną zwrócone żadne dane, [Opublikuj](#post) dane w aplikacji.

## <a name="routing-and-url-paths"></a>Ścieżki routingu i adresów URL

Ten [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) atrybut oznacza metodę, która reaguje na żądanie HTTP GET. Ścieżka adresu URL dla każdej metody jest zbudowana w następujący sposób:

* Zacznij od ciągu szablonu w `Route` atrybucie kontrolera:

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* Zastąp `[controller]` nazwą kontrolera, którą Konwencją jest nazwa klasy kontrolera minus sufiks "Controller". Dla tego przykładu nazwa klasy kontrolera to **TodoItems** Controller, więc nazwa kontrolera to "TodoItems". W ASP.NET Core [routingu](xref:mvc/controllers/routing) jest rozróżniana wielkość liter.
* Jeśli `[HttpGet]` atrybut ma szablon trasy (na przykład `[HttpGet("products")]` ), Dodaj go do ścieżki. Ten przykład nie używa szablonu. Aby uzyskać więcej informacji, zobacz temat [Routing atrybutów z atrybutami http [Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).

W poniższej `GetTodoItem` metodzie `"{id}"` jest zmienną zastępczą dla unikatowego identyfikatora elementu do wykonania. Gdy `GetTodoItem` jest wywoływana, wartość `"{id}"` w adresie URL jest podawana do metody w jej `id` parametrze.

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a>Wartości zwracane

Zwracany typ `GetTodoItems` `GetTodoItem` metod i jest [ \<T> typem ActionResult](xref:web-api/action-return-types#actionresultt-type). ASP.NET Core automatycznie serializować obiektu do [formatu JSON](https://www.json.org/) i zapisuje kod JSON w treści komunikatu odpowiedzi. Kod odpowiedzi dla tego typu zwracanego to [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), przy założeniu, że nie istnieją Nieobsłużone wyjątki. Nieobsłużone wyjątki są tłumaczone na błędy 5xx.

`ActionResult` typy zwracane mogą reprezentować szeroką gamę kodów stanu HTTP. Na przykład `GetTodoItem` może zwracać dwie różne wartości stanu:

* Jeśli żaden element nie jest zgodny z żądanym IDENTYFIKATORem, metoda zwraca kod błędu [stanu 404](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> .
* W przeciwnym razie metoda zwraca 200 z treścią odpowiedzi JSON. Zwracanie `item` wyników w odpowiedzi HTTP 200.

## <a name="the-puttodoitem-method"></a>Metoda PutTodoItem

Przeanalizuj metodę `PutTodoItem`:

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

`PutTodoItem` jest podobny do `PostTodoItem` , z tą różnicą, że używa protokołu HTTP Put. Odpowiedź to [204 (brak zawartości)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html). Zgodnie ze specyfikacją protokołu HTTP żądanie PUT wymaga, aby klient wysłał całą zaktualizowaną jednostkę, a nie tylko te zmiany. Aby zapewnić obsługę częściowych aktualizacji, użyj [poprawki http](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).

Jeśli wystąpi błąd podczas wywoływania `PutTodoItem` , wywołaj, `GET` Aby upewnić się, że w bazie danych znajduje się element.

### <a name="test-the-puttodoitem-method"></a>Testowanie metody PutTodoItem

Ten przykład korzysta z bazy danych w pamięci, która musi zostać zainicjowana za każdym razem, gdy aplikacja zostanie uruchomiona. Przed wykonaniem wywołania PUT musi istnieć element w bazie danych. Przed wykonaniem wywołania PUT należy wywołać metodę GET, aby upewnić się, że istnieje element w bazie danych.

Zaktualizuj element do wykonania o identyfikatorze 1 i ustaw jego nazwę na `"feed fish"` :

```json
  {
    "Id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

Na poniższej ilustracji przedstawiono aktualizację programu Poster:

![Konsola programu Poster pokazująca odpowiedź 204 (brak zawartości)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a>Metoda DeleteTodoItem

Przeanalizuj metodę `DeleteTodoItem`:

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a>Testowanie metody DeleteTodoItem

Użyj programu Poster, aby usunąć element do wykonania:

* Ustaw metodę na `DELETE` .
* Ustaw identyfikator URI obiektu do usunięcia (na przykład `https://localhost:5001/api/TodoItems/1` ).
* Wybierz pozycję **Wyślij**.

<a name="over-post-v5"></a>

## <a name="prevent-over-posting"></a>Zapobiegaj za pośrednictwem księgowania

Obecnie Przykładowa aplikacja uwidacznia cały `TodoItem` obiekt. Aplikacje produkcyjne zwykle ograniczają dane wejściowe i zwracane przy użyciu podzestawu modelu. Istnieje wiele powodów związanych z tym, a zabezpieczenia są głównymi. Podzestaw modelu jest zwykle określany jako obiekt Transfer danych (DTO), model wejściowy lub model widoku. **DTO** jest używany w tym artykule.

DTO może służyć do:

* Zablokuj nadmierne księgowanie.
* Ukryj właściwości, które nie powinny być wyświetlane dla klientów.
* Pomiń niektóre właściwości, aby zmniejszyć rozmiar ładunku.
* Spłaszcz wykresy obiektów zawierające obiekty zagnieżdżone. Spłaszczone wykresy obiektów mogą być wygodniejsze dla klientów.

Aby zademonstrować podejście DTO, zaktualizuj `TodoItem` klasę w celu uwzględnienia pola tajnego:

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=8)]

Pole tajne musi być ukryte w tej aplikacji, ale aplikacja administracyjna mogła ją uwidocznić.

Sprawdź, czy można opublikować i pobrać pole tajne.

Utwórz model DTO:

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

Zaktualizuj, `TodoItemsController` Aby użyć `TodoItemDTO` :

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

Upewnij się, że nie można opublikować lub pobrać pola tajnego.

## <a name="call-the-web-api-with-javascript"></a>Wywoływanie interfejsu API sieci Web przy użyciu języka JavaScript

Zobacz [Samouczek: wywoływanie interfejsu API sieci web ASP.NET Core przy użyciu języka JavaScript](xref:tutorials/web-api-javascript).

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Utwórz projekt interfejsu API sieci Web.
> * Dodaj klasę modelu i kontekst bazy danych.
> * Tworzy szkielet kontrolera z metodami CRUD.
> * Skonfiguruj Routing, ścieżki URL i wartości zwracane.
> * Wywołaj interfejs API sieci Web za pomocą programu Poster.

Na końcu znajduje się internetowy interfejs API, który może zarządzać elementami do wykonania przechowywanymi w bazie danych.

## <a name="overview"></a>Omówienie

Ten samouczek tworzy następujący interfejs API:

|Interfejs API | Opis | Treść żądania | Treść odpowiedzi |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | Pobierz wszystkie elementy do wykonania | Brak | Tablica elementów do wykonania|
|`GET /api/TodoItems/{id}` | Pobieranie elementu według identyfikatora | Brak | Element do wykonania|
|`POST /api/TodoItems` | Dodaj nowy element | Element do wykonania | Element do wykonania |
|`PUT /api/TodoItems/{id}` | Aktualizowanie istniejącego elementu &nbsp; | Element do wykonania | Brak |
|`DELETE /api/TodoItems/{id}` &nbsp; &nbsp; | Usuń element &nbsp;&nbsp; | Brak | Brak|

Na poniższym diagramie przedstawiono projekt aplikacji.

![Klient jest reprezentowany przez pole po lewej stronie. Przesyła żądanie i odbiera odpowiedź z aplikacji, pole rysowane po prawej stronie. W polu aplikacja trzy pola reprezentują kontroler, model i warstwę dostępu do danych. Żądanie znajduje się w kontrolerze aplikacji, a operacje odczytu i zapisu są wykonywane między kontrolerem a warstwą dostępu do danych. Model jest serializowany i zwracany do klienta w odpowiedzi.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a>Tworzenie projektu sieci Web

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Z menu **plik** wybierz pozycję **Nowy** > **projekt**.
* Wybierz szablon **aplikacja sieci Web ASP.NET Core** a następnie kliknij przycisk **dalej**.
* Nazwij projekt *TodoApi* i kliknij pozycję **Utwórz**.
* W oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** upewnij się, że wybrano opcję **.net Core** i **ASP.NET Core 3,1** . Wybierz szablon **interfejsu API** i kliknij przycisk **Utwórz**.

![Okno dialogowe programu VS New Project](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Zmień katalog ( `cd` ) do folderu, który będzie zawierać folder projektu.
* Uruchom następujące polecenia:

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* Gdy zostanie wyświetlone okno dialogowe z pytaniem, czy chcesz dodać wymagane zasoby do projektu, wybierz opcję **tak**.

  Poprzednie polecenia:

  * Tworzy nowy projekt internetowego interfejsu API i otwiera go w Visual Studio Code.
  * Dodaje pakiety NuGet, które są wymagane w następnej sekcji.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* Wybierz pozycję **plik** > **nowe rozwiązanie**.

  ![macOS nowe rozwiązanie](first-web-api-mac/_static/sln.png)

* W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz **.NET Core** pozycję  >  **App**  >  **interfejs API** aplikacji .NET Core  >  **Next**. W wersji 8,6 lub nowszej wybierz pozycję **Web and Console**  >  **App**  >  **interfejs API**  >  **Next** aplikacji sieci Web i konsoli.

  ![Wybór szablonu interfejsu API macOS](first-web-api-mac/_static/api_template.png)

* W oknie dialogowym **Konfigurowanie nowego interfejsu API sieci Web ASP.NET Core** wybierz najnowszą platformę **docelową**.NET Core 3. x. Wybierz pozycję **Dalej**.

* Wprowadź *TodoApi* jako **nazwę projektu** , a następnie wybierz pozycję **Utwórz**.

  ![okno dialogowe konfiguracji](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

Otwórz Terminal poleceń w folderze projektu i uruchom następujące polecenia:

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a>Testowanie interfejsu API

Szablon projektu tworzy `WeatherForecast` interfejs API. Wywołaj `Get` metodę z przeglądarki, aby przetestować aplikację.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Naciśnij klawisze CTRL + F5, aby uruchomić aplikację. Program Visual Studio uruchamia przeglądarkę i przechodzi do `https://localhost:<port>/WeatherForecast` lokalizacji, gdzie `<port>` jest losowo wybierany numer portu.

Jeśli zostanie wyświetlone okno dialogowe z pytaniem, czy należy zaufać certyfikatowi IIS Express, wybierz pozycję **tak**. W wyświetlonym oknie dialogowym **ostrzeżenia o zabezpieczeniach** wybierz pozycję **tak**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Naciśnij klawisze CTRL + F5, aby uruchomić aplikację. W przeglądarce przejdź do następującego adresu URL: `https://localhost:5001/WeatherForecast` .

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

Wybierz pozycję **Uruchom**  >  **Rozpocznij debugowanie** , aby uruchomić aplikację. Visual Studio dla komputerów Mac uruchamia przeglądarkę i nawiguje do `https://localhost:<port>` , gdzie `<port>` jest losowo wybranym numerem portu. Zwracany jest błąd HTTP 404 (nie znaleziono). Dołącz `/WeatherForecast` do adresu URL (Zmień adres URL na `https://localhost:<port>/WeatherForecast` ).

---

Zwracany jest kod JSON podobny do następującego:

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

## <a name="add-a-model-class"></a>Dodaj klasę modelu

*Model* to zestaw klas, które reprezentują dane zarządzane przez aplikację. Model tej aplikacji jest pojedynczą `TodoItem` klasą.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt. Wybierz pozycję **Dodaj**  >  **Nowy folder**. Nazwij folder *Models* .

* Kliknij prawym przyciskiem myszy *Models* folder i wybierz polecenie **Dodaj**  >  **klasę**. Nadaj klasie nazwę *TodoItem* i wybierz pozycję **Dodaj**.

* Zastąp kod szablonu następującym kodem:

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Dodaj folder o nazwie *Models* .

* Dodaj `TodoItem` do *Models* folderu klasę o następującym kodzie:

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* Kliknij prawym przyciskiem myszy projekt. Wybierz pozycję **Dodaj**  >  **Nowy folder**. Nazwij folder *Models* .

  ![Nowy folder](first-web-api-mac/_static/folder.png)

* Kliknij prawym przyciskiem myszy *Models* folder, a następnie wybierz pozycję **Dodaj** > **nowy plik** > **ogólna** > **pusta Klasa**.

* Nazwij klasę *TodoItem*, a następnie kliknij pozycję **New (nowy**).

* Zastąp kod szablonu następującym kodem:

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

`Id`Właściwość działa jako unikatowy klucz w relacyjnej bazie danych.

Klasy modelu mogą przejść do dowolnego miejsca w projekcie, ale *Models* folder jest używany przez Konwencję.

## <a name="add-a-database-context"></a>Dodawanie kontekstu bazy danych

*Kontekst bazy danych* jest główną klasą, która koordynuje Entity Framework funkcji dla modelu danych. Ta klasa jest tworzona przez wyprowadzanie z `Microsoft.EntityFrameworkCore.DbContext` klasy.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="add-nuget-packages"></a>Dodawanie pakietów NuGet

* W menu **Narzędzia** wybierz pozycję **menedżer pakietów NuGet > zarządzanie pakietami NuGet dla rozwiązania**.
* Wybierz kartę **Przeglądaj** , a następnie w polu wyszukiwania wprowadź ciąg **Microsoft. EntityFrameworkCore. SqlServer** .
* W lewym okienku wybierz pozycję **Microsoft. EntityFrameworkCore. SqlServer** .
* Zaznacz pole wyboru **projekt** w prawym okienku, a następnie wybierz pozycję **Zainstaluj**.
* Użyj powyższych instrukcji, aby dodać pakiet NuGet **Microsoft. EntityFrameworkCore. inMemory** .

![Menedżer pakietów NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a>Dodawanie kontekstu bazy danych TodoContext

* Kliknij prawym przyciskiem myszy *Models* folder i wybierz polecenie **Dodaj**  >  **klasę**. Nadaj klasie nazwę *TodoContext* i kliknij przycisk **Dodaj**.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

* Dodaj `TodoContext` klasę do *Models* folderu.

---

* Wprowadź następujący kod:

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a>Rejestrowanie kontekstu bazy danych

W ASP.NET Core usługi, takie jak kontekst bazy danych, muszą być zarejestrowane z kontenerem [iniekcji zależności (di)](xref:fundamentals/dependency-injection) . Kontener udostępnia usługę kontrolerom.

Zaktualizuj *Startup.cs* o następujący wyróżniony kod:

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

Powyższy kod ma następujące działanie:

* Usuwa nieużywane `using` deklaracje.
* Dodaje kontekst bazy danych do kontenera DI.
* Określa, że kontekst bazy danych będzie używać bazy danych w pamięci.

## <a name="scaffold-a-controller"></a>Tworzenie szkieletu kontrolera

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Kliknij prawym przyciskiem myszy folder *controllers* .
* Wybierz pozycję **Dodaj** > **nowy element szkieletowy**.
* Wybierz pozycję **kontroler interfejsu API z akcjami, używając Entity Framework**, a następnie wybierz pozycję **Dodaj**.
* Na stronie **Dodawanie kontrolera interfejsu API z akcjami przy użyciu Entity Framework** dialogowego:

  * Wybierz pozycję **TodoItem (TodoApi. Models )** w **klasie model**.
  * Wybierz pozycję **TodoContext (TodoApi. Models )** w **klasie kontekstu danych**.
  * Wybierz pozycję **Dodaj**.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

Uruchom następujące polecenia:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

Poprzednie polecenia:

* Dodaj pakiety NuGet wymagane do tworzenia szkieletów.
* Instaluje aparat tworzenia szkieletu ( `dotnet-aspnet-codegenerator` ).
* Szkielety `TodoItemsController` .

---

Wygenerowany kod:

* Oznacza klasę [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atrybutem. Ten atrybut wskazuje, że kontroler odpowiada na żądania interfejsu API sieci Web. Aby uzyskać informacje o określonych zachowaniach, które włącza atrybut, zobacz <xref:web-api/index> .
* Używa funkcji DI do iniekcji kontekstu bazy danych ( `TodoContext` ) do kontrolera. Kontekst bazy danych jest używany w każdej z metod [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) w kontrolerze.

Szablony ASP.NET Core dla:

* Kontrolery z widokami obejmują `[action]` szablon trasy.
* Kontrolery interfejsu API nie należą `[action]` do szablonu trasy.

Gdy `[action]` token nie znajduje się w szablonie trasy, nazwa [akcji](xref:mvc/controllers/routing#action) jest wykluczona z trasy. Oznacza to, że nazwa metody skojarzonej z akcją nie jest używana w zgodnej trasie.

## <a name="examine-the-posttodoitem-create-method"></a>Badanie metody PostTodoItem Create

Zastąp instrukcję return w, `PostTodoItem` Aby użyć operatora [nameof](/dotnet/csharp/language-reference/operators/nameof) :

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

Poprzedni kod jest metodą POST protokołu HTTP, jak wskazano w [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) atrybucie. Metoda pobiera wartość elementu do wykonania z treści żądania HTTP.

Aby uzyskać więcej informacji, zobacz temat [Routing atrybutów z atrybutami http [Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).

<xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>Metoda:

* W razie powodzenia zwraca kod stanu HTTP 201. HTTP 201 to standardowa odpowiedź dla metody POST protokołu HTTP, która tworzy nowy zasób na serwerze.
* Dodaje nagłówek [lokalizacji](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) do odpowiedzi. `Location`Nagłówek określa [Identyfikator URI](https://developer.mozilla.org/docs/Glossary/URI) nowo utworzonego elementu do wykonania. Aby uzyskać więcej informacji, zobacz [10.2.2 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).
* Odwołuje się do `GetTodoItem` akcji tworzenia `Location` identyfikatora URI nagłówka. `nameof`Słowo kluczowe języka C# służy do zapobiegania twardemu kodowaniu nazwy akcji w `CreatedAtAction` wywołaniu.

### <a name="install-postman"></a>Zainstaluj program Poster

Ten samouczek używa programu do testowania interfejsu API sieci Web.

* Zainstaluj program [Poster](https://www.getpostman.com/downloads/)
* Uruchom aplikację internetową.
* Uruchom wpis.
* Wyłącz **weryfikację certyfikatu SSL**
  * W **File** obszarze > **Ustawienia** pliku (karta **Ogólne** ) Wyłącz **weryfikację certyfikatu SSL**.
    > [!WARNING]
    > Po przetestowaniu kontrolera ponownie Włącz weryfikację certyfikatu SSL.

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a>Test PostTodoItem za pomocą programu Poster

* Utwórz nowe żądanie.
* Ustaw metodę HTTP na `POST` .
* Ustaw identyfikator URI na `https://localhost:<port>/api/TodoItems` . Na przykład `https://localhost:5001/api/TodoItems`.
* Wybierz kartę **Treść**.
* Wybierz przycisk radiowy **RAW** .
* Ustaw typ na **JSON (Application/JSON)**.
* W treści żądania wprowadź kod JSON dla elementu do wykonania:

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* Wybierz pozycję **Wyślij**.

  ![Ogłoś przy użyciu żądania Create](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a>Testowanie identyfikatora URI nagłówka lokalizacji za pomocą programu Poster

* Wybierz kartę **nagłówki** w okienku **odpowiedź** .
* Skopiuj wartość nagłówka **lokalizacji** :

  ![Karta nagłówki w konsoli programu Poster](first-web-api/_static/3/create.png)

* Ustaw metodę HTTP na `GET` .
* Ustaw identyfikator URI na `https://localhost:<port>/api/TodoItems/1` . Na przykład `https://localhost:5001/api/TodoItems/1`.
* Wybierz pozycję **Wyślij**.

## <a name="examine-the-get-methods"></a>Badanie metod GET

Te metody implementują dwa punkty końcowe GET:

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

Przetestuj aplikację, wywołując dwa punkty końcowe z przeglądarki lub wpisu. Na przykład:

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

Odpowiedź podobna do poniższego jest generowana przez wywołanie `GetTodoItems` :

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a>Test get przy użyciu programu Poster

* Utwórz nowe żądanie.
* Ustaw metodę HTTP, aby **uzyskać**.
* Ustaw identyfikator URI żądania na `https://localhost:<port>/api/TodoItems` . Na przykład `https://localhost:5001/api/TodoItems`.
* Ustaw **dwa widoki okienka** w programie Poster.
* Wybierz pozycję **Wyślij**.

Ta aplikacja używa bazy danych w pamięci. Jeśli aplikacja zostanie zatrzymana i uruchomiona, poprzednie żądanie GET nie zwróci żadnych danych. Jeśli nie zostaną zwrócone żadne dane, [Opublikuj](#post) dane w aplikacji.

## <a name="routing-and-url-paths"></a>Ścieżki routingu i adresów URL

Ten [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) atrybut oznacza metodę, która reaguje na żądanie HTTP GET. Ścieżka adresu URL dla każdej metody jest zbudowana w następujący sposób:

* Zacznij od ciągu szablonu w `Route` atrybucie kontrolera:

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* Zastąp `[controller]` nazwą kontrolera, którą Konwencją jest nazwa klasy kontrolera minus sufiks "Controller". Dla tego przykładu nazwa klasy kontrolera to **TodoItems** Controller, więc nazwa kontrolera to "TodoItems". W ASP.NET Core [routingu](xref:mvc/controllers/routing) jest rozróżniana wielkość liter.
* Jeśli `[HttpGet]` atrybut ma szablon trasy (na przykład `[HttpGet("products")]` ), Dodaj go do ścieżki. Ten przykład nie używa szablonu. Aby uzyskać więcej informacji, zobacz temat [Routing atrybutów z atrybutami http [Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).

W poniższej `GetTodoItem` metodzie `"{id}"` jest zmienną zastępczą dla unikatowego identyfikatora elementu do wykonania. Gdy `GetTodoItem` jest wywoływana, wartość `"{id}"` w adresie URL jest podawana do metody w jej `id` parametrze.

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a>Wartości zwracane 

Zwracany typ `GetTodoItems` `GetTodoItem` metod i jest [ \<T> typem ActionResult](xref:web-api/action-return-types#actionresultt-type). ASP.NET Core automatycznie serializować obiektu do [formatu JSON](https://www.json.org/) i zapisuje kod JSON w treści komunikatu odpowiedzi. Kod odpowiedzi dla tego typu zwracanego to 200, przy założeniu, że nie istnieją Nieobsłużone wyjątki. Nieobsłużone wyjątki są tłumaczone na błędy 5xx.

`ActionResult` typy zwracane mogą reprezentować szeroką gamę kodów stanu HTTP. Na przykład `GetTodoItem` może zwracać dwie różne wartości stanu:

* Jeśli żaden element nie jest zgodny z żądanym IDENTYFIKATORem, metoda zwróci <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> Kod błędu 404.
* W przeciwnym razie metoda zwraca 200 z treścią odpowiedzi JSON. Zwracanie `item` wyników w odpowiedzi HTTP 200.

## <a name="the-puttodoitem-method"></a>Metoda PutTodoItem

Przeanalizuj metodę `PutTodoItem`:

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

`PutTodoItem` jest podobny do `PostTodoItem` , z tą różnicą, że używa protokołu HTTP Put. Odpowiedź to [204 (brak zawartości)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html). Zgodnie ze specyfikacją protokołu HTTP żądanie PUT wymaga, aby klient wysłał całą zaktualizowaną jednostkę, a nie tylko te zmiany. Aby zapewnić obsługę częściowych aktualizacji, użyj [poprawki http](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).

Jeśli wystąpi błąd podczas wywoływania `PutTodoItem` , wywołaj, `GET` Aby upewnić się, że w bazie danych znajduje się element.

### <a name="test-the-puttodoitem-method"></a>Testowanie metody PutTodoItem

Ten przykład korzysta z bazy danych w pamięci, która musi zostać zainicjowana za każdym razem, gdy aplikacja zostanie uruchomiona. Przed wykonaniem wywołania PUT musi istnieć element w bazie danych. Przed wykonaniem wywołania PUT należy wywołać metodę GET, aby upewnić się, że istnieje element w bazie danych.

Zaktualizuj element do wykonania o identyfikatorze 1 i ustaw jego nazwę na "Źródło danych":

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

Na poniższej ilustracji przedstawiono aktualizację programu Poster:

![Konsola programu Poster pokazująca odpowiedź 204 (brak zawartości)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a>Metoda DeleteTodoItem

Przeanalizuj metodę `DeleteTodoItem`:

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a>Testowanie metody DeleteTodoItem

Użyj programu Poster, aby usunąć element do wykonania:

* Ustaw metodę na `DELETE` .
* Ustaw identyfikator URI obiektu do usunięcia (na przykład `https://localhost:5001/api/TodoItems/1` ).
* Wybierz pozycję **Wyślij**.

<a name="over-post"></a>
<a name="over-post-v3"></a>

## <a name="prevent-over-posting"></a>Zapobiegaj za pośrednictwem księgowania

Obecnie Przykładowa aplikacja uwidacznia cały `TodoItem` obiekt. Aplikacje produkcyjne zwykle ograniczają dane wejściowe i zwracane przy użyciu podzestawu modelu. Istnieje wiele powodów związanych z tym, a zabezpieczenia są głównymi. Podzestaw modelu jest zwykle określany jako obiekt Transfer danych (DTO), model wejściowy lub model widoku. **DTO** jest używany w tym artykule.

DTO może służyć do:

* Zablokuj nadmierne księgowanie.
* Ukryj właściwości, które nie powinny być wyświetlane dla klientów.
* Pomiń niektóre właściwości, aby zmniejszyć rozmiar ładunku.
* Spłaszcz wykresy obiektów zawierające obiekty zagnieżdżone. Spłaszczone wykresy obiektów mogą być wygodniejsze dla klientów.

Aby zademonstrować podejście DTO, zaktualizuj `TodoItem` klasę w celu uwzględnienia pola tajnego:

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

Pole tajne musi być ukryte w tej aplikacji, ale aplikacja administracyjna mogła ją uwidocznić.

Sprawdź, czy można opublikować i pobrać pole tajne.

Utwórz model DTO:

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

Zaktualizuj, `TodoItemsController` Aby użyć `TodoItemDTO` :

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

Upewnij się, że nie można opublikować lub pobrać pola tajnego.

## <a name="call-the-web-api-with-javascript"></a>Wywoływanie interfejsu API sieci Web przy użyciu języka JavaScript

Zobacz [Samouczek: wywoływanie interfejsu API sieci web ASP.NET Core przy użyciu języka JavaScript](xref:tutorials/web-api-javascript).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Utwórz projekt interfejsu API sieci Web.
> * Dodaj klasę modelu i kontekst bazy danych.
> * Dodaj kontroler.
> * Dodaj metody CRUD.
> * Skonfiguruj ścieżki routingu i adresów URL.
> * Określ wartości zwracane.
> * Wywołaj interfejs API sieci Web za pomocą programu Poster.
> * Wywołaj interfejs API sieci Web za pomocą języka JavaScript.

Na końcu znajduje się internetowy interfejs API, który może zarządzać elementami do wykonania przechowywanymi w relacyjnej bazie danych.

## <a name="overview-21"></a>Przegląd 2,1

Ten samouczek tworzy następujący interfejs API:

|Interfejs API | Opis | Treść żądania | Treść odpowiedzi |
|--- | ---- | ---- | ---- |
|Pobierz/api/TodoItems | Pobierz wszystkie elementy do wykonania | Brak | Tablica elementów do wykonania|
|Pobierz/api/TodoItems/{id} | Pobieranie elementu według identyfikatora | Brak | Element do wykonania|
|Opublikuj/api/TodoItems | Dodaj nowy element | Element do wykonania | Element do wykonania |
|Umieść/api/TodoItems/{id} | Aktualizowanie istniejącego elementu &nbsp; | Element do wykonania | Brak |
|Usuń/api/TodoItems/{id} &nbsp;&nbsp; | Usuń element &nbsp;&nbsp; | Brak | Brak|

Na poniższym diagramie przedstawiono projekt aplikacji.

![Klient jest reprezentowany przez pole po lewej stronie. Przesyła żądanie i odbiera odpowiedź z aplikacji, pole rysowane po prawej stronie. W polu aplikacja trzy pola reprezentują kontroler, model i warstwę dostępu do danych. Żądanie znajduje się w kontrolerze aplikacji, a operacje odczytu i zapisu są wykonywane między kontrolerem a warstwą dostępu do danych. Model jest serializowany i zwracany do klienta w odpowiedzi.](first-web-api/_static/architecture.png)

## <a name="prerequisites-21"></a>Wymagania wstępne 2,1

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project-21"></a>Tworzenie projektu sieci Web 2,1

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Z menu **plik** wybierz pozycję **Nowy** > **projekt**.
* Wybierz szablon **aplikacja sieci Web ASP.NET Core** a następnie kliknij przycisk **dalej**.
* Nazwij projekt *TodoApi* i kliknij pozycję **Utwórz**.
* W oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** upewnij się, że wybrano opcję **.net Core** i **ASP.NET Core 2,2** . Wybierz szablon **interfejsu API** i kliknij przycisk **Utwórz**. **Nie** zaznaczaj opcji **Włącz obsługę platformy Docker**.

![Okno dialogowe programu VS New Project](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Zmień katalog ( `cd` ) do folderu, który będzie zawierać folder projektu.
* Uruchom następujące polecenia:

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  Te polecenia tworzą nowy projekt internetowego interfejsu API i otwierają nowe wystąpienie Visual Studio Code w nowym folderze projektu.

* Gdy zostanie wyświetlone okno dialogowe z pytaniem, czy chcesz dodać wymagane zasoby do projektu, wybierz opcję **tak**.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* Wybierz pozycję **plik** > **nowe rozwiązanie**.

  ![macOS nowe rozwiązanie](first-web-api-mac/_static/sln.png)

* W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz **.NET Core** pozycję  >  **App**  >  **interfejs API** aplikacji .NET Core  >  **Next**. W wersji 8,6 lub nowszej wybierz pozycję **Web and Console**  >  **App**  >  **interfejs API**  >  **Next** aplikacji sieci Web i konsoli.
  
* W oknie dialogowym **Konfigurowanie nowego interfejsu API sieci Web ASP.NET Core** wybierz najnowszą platformę **docelową** programu .NET Core 2. x. Wybierz pozycję **Dalej**.

* Wprowadź *TodoApi* jako **nazwę projektu** , a następnie wybierz pozycję **Utwórz**.

  ![okno dialogowe konfiguracji](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api-21"></a>Testowanie interfejsu API 2,1

Szablon projektu tworzy `values` interfejs API. Wywołaj `Get` metodę z przeglądarki, aby przetestować aplikację.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Naciśnij klawisze CTRL + F5, aby uruchomić aplikację. Program Visual Studio uruchamia przeglądarkę i przechodzi do `https://localhost:<port>/api/values` lokalizacji, gdzie `<port>` jest losowo wybierany numer portu.

Jeśli zostanie wyświetlone okno dialogowe z pytaniem, czy należy zaufać certyfikatowi IIS Express, wybierz pozycję **tak**. W wyświetlonym oknie dialogowym **ostrzeżenia o zabezpieczeniach** wybierz pozycję **tak**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Naciśnij klawisze CTRL + F5, aby uruchomić aplikację. W przeglądarce przejdź do następującego adresu URL: `https://localhost:5001/api/values` .

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

Wybierz pozycję **Uruchom**  >  **Rozpocznij debugowanie** , aby uruchomić aplikację. Visual Studio dla komputerów Mac uruchamia przeglądarkę i nawiguje do `https://localhost:<port>` , gdzie `<port>` jest losowo wybranym numerem portu. Zwracany jest błąd HTTP 404 (nie znaleziono). Dołącz `/api/values` do adresu URL (Zmień adres URL na `https://localhost:<port>/api/values` ).

---

Zostanie zwrócony następujący kod JSON:

```json
["value1","value2"]
```

## <a name="add-a-model-class-21"></a>Dodaj klasę modelu 2,1

*Model* to zestaw klas, które reprezentują dane zarządzane przez aplikację. Model tej aplikacji jest pojedynczą `TodoItem` klasą.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt. Wybierz pozycję **Dodaj**  >  **Nowy folder**. Nazwij folder *Models* .

* Kliknij prawym przyciskiem myszy *Models* folder i wybierz polecenie **Dodaj**  >  **klasę**. Nadaj klasie nazwę *TodoItem* i wybierz pozycję **Dodaj**.

* Zastąp kod szablonu następującym kodem:

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Dodaj folder o nazwie *Models* .

* Dodaj `TodoItem` do *Models* folderu klasę o następującym kodzie:

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* Kliknij prawym przyciskiem myszy projekt. Wybierz pozycję **Dodaj**  >  **Nowy folder**. Nazwij folder *Models* .

  ![Nowy folder](first-web-api-mac/_static/folder.png)

* Kliknij prawym przyciskiem myszy *Models* folder, a następnie wybierz pozycję **Dodaj** > **nowy plik** > **ogólna** > **pusta Klasa**.

* Nazwij klasę *TodoItem*, a następnie kliknij pozycję **New (nowy**).

* Zastąp kod szablonu następującym kodem:

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

`Id`Właściwość działa jako unikatowy klucz w relacyjnej bazie danych.

Klasy modelu mogą przejść do dowolnego miejsca w projekcie, ale *Models* folder jest używany przez Konwencję.

## <a name="add-a-database-context-21"></a>Dodawanie kontekstu bazy danych 2,1

*Kontekst bazy danych* jest główną klasą, która koordynuje Entity Framework funkcji dla modelu danych. Ta klasa jest tworzona przez wyprowadzanie z `Microsoft.EntityFrameworkCore.DbContext` klasy.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Kliknij prawym przyciskiem myszy *Models* folder i wybierz polecenie **Dodaj**  >  **klasę**. Nadaj klasie nazwę *TodoContext* i kliknij przycisk **Dodaj**.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

* Dodaj `TodoContext` klasę do *Models* folderu.

---

* Zastąp kod szablonu następującym kodem:

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context-21"></a>Rejestrowanie kontekstu bazy danych 2,1

W ASP.NET Core usługi, takie jak kontekst bazy danych, muszą być zarejestrowane z kontenerem [iniekcji zależności (di)](xref:fundamentals/dependency-injection) . Kontener udostępnia usługę kontrolerom.

Zaktualizuj *Startup.cs* o następujący wyróżniony kod:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

Powyższy kod ma następujące działanie:

* Usuwa nieużywane `using` deklaracje.
* Dodaje kontekst bazy danych do kontenera DI.
* Określa, że kontekst bazy danych będzie używać bazy danych w pamięci.

## <a name="add-a-controller-21"></a>Dodawanie kontrolera 2,1

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Kliknij prawym przyciskiem myszy folder *controllers* .
* Wybierz pozycję **Dodaj** > **nowy element**.
* W oknie dialogowym **Dodaj nowy element** wybierz szablon **Klasa kontrolera interfejsu API** .
* Nadaj klasie nazwę *TodoController* i wybierz pozycję **Dodaj**.

  ![Okno dialogowe Dodawanie nowego elementu z kontrolerem w polu wyszukiwania i wybranym kontrolerem interfejsu API sieci Web](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

* W folderze *controllers* Utwórz klasę o nazwie `TodoController` .

---

* Zastąp kod szablonu następującym kodem:

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

Powyższy kod ma następujące działanie:

* Definiuje klasę kontrolera interfejsu API bez metod.
* Oznacza klasę [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atrybutem. Ten atrybut wskazuje, że kontroler odpowiada na żądania interfejsu API sieci Web. Aby uzyskać informacje o określonych zachowaniach, które włącza atrybut, zobacz <xref:web-api/index> .
* Używa funkcji DI do iniekcji kontekstu bazy danych ( `TodoContext` ) do kontrolera. Kontekst bazy danych jest używany w każdej z metod [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) w kontrolerze.
* Dodaje element o nazwie `Item1` do bazy danych, jeśli baza danych jest pusta. Ten kod znajduje się w konstruktorze, więc jest uruchamiany za każdym razem, gdy istnieje nowe żądanie HTTP. Jeśli usuniesz wszystkie elementy, Konstruktor zostanie ponownie utworzony `Item1` przy następnym wywołaniu metody interfejsu API. Dlatego może wyglądać podobnie jak usunięcie nie zadziałało, gdy rzeczywiście zadziałało.

## <a name="add-get-methods-21"></a>Dodaj metody get 2,1

Aby udostępnić interfejs API, który pobiera elementy do wykonania, Dodaj następujące metody do `TodoController` klasy:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

Te metody implementują dwa punkty końcowe GET:

* `GET /api/todo`
* `GET /api/todo/{id}`

Zatrzymaj aplikację, jeśli jest nadal uruchomiona. Następnie uruchom ją ponownie, aby uwzględnić najnowsze zmiany.

Przetestuj aplikację, wywołując dwa punkty końcowe z przeglądarki. Na przykład:

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

Następująca odpowiedź HTTP jest generowana przez wywołanie `GetTodoItems` :

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths-21"></a>Ścieżki routingu i adresu URL 2,1

Ten [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) atrybut oznacza metodę, która reaguje na żądanie HTTP GET. Ścieżka adresu URL dla każdej metody jest zbudowana w następujący sposób:

* Zacznij od ciągu szablonu w `Route` atrybucie kontrolera:

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* Zastąp `[controller]` nazwą kontrolera, którą Konwencją jest nazwa klasy kontrolera minus sufiks "Controller". W przypadku tego przykładu nazwa klasy kontrolera to kontroler do **zrobienia**, więc nazwa kontrolera to "do zrobienia". W ASP.NET Core [routingu](xref:mvc/controllers/routing) jest rozróżniana wielkość liter.
* Jeśli `[HttpGet]` atrybut ma szablon trasy (na przykład `[HttpGet("products")]` ), Dodaj go do ścieżki. Ten przykład nie używa szablonu. Aby uzyskać więcej informacji, zobacz temat [Routing atrybutów z atrybutami http [Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).

W poniższej `GetTodoItem` metodzie `"{id}"` jest zmienną zastępczą dla unikatowego identyfikatora elementu do wykonania. Gdy `GetTodoItem` jest wywoływana, wartość `"{id}"` w adresie URL jest podawana do metody w jej `id` parametrze.

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values-21"></a>Wartości zwracane 2,1

Zwracany typ `GetTodoItems` `GetTodoItem` metod i jest [ \<T> typem ActionResult](xref:web-api/action-return-types#actionresultt-type). ASP.NET Core automatycznie serializować obiektu do [formatu JSON](https://www.json.org/) i zapisuje kod JSON w treści komunikatu odpowiedzi. Kod odpowiedzi dla tego typu zwracanego to 200, przy założeniu, że nie istnieją Nieobsłużone wyjątki. Nieobsłużone wyjątki są tłumaczone na błędy 5xx.

`ActionResult` typy zwracane mogą reprezentować szeroką gamę kodów stanu HTTP. Na przykład `GetTodoItem` może zwracać dwie różne wartości stanu:

* Jeśli żaden element nie jest zgodny z żądanym IDENTYFIKATORem, metoda zwróci <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> Kod błędu 404.
* W przeciwnym razie metoda zwraca 200 z treścią odpowiedzi JSON. Zwracanie `item` wyników w odpowiedzi HTTP 200.

## <a name="test-the-gettodoitems-method-21"></a>Testowanie metody GetTodoItems 2,1

Ten samouczek używa programu do testowania interfejsu API sieci Web.

* Zainstaluj program [Poster](https://www.getpostman.com/downloads/).
* Uruchom aplikację internetową.
* Uruchom wpis.
* Wyłącz **weryfikację certyfikatu SSL**.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* W **File** obszarze > **Ustawienia** pliku (karta **Ogólne** ) Wyłącz **weryfikację certyfikatu SSL**.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

* Z poziomu preferencji programu **Poster**  >  **Preferences** (karta **Ogólne** ) Wyłącz **weryfikację certyfikatu SSL**. Alternatywnie wybierz klucz i wybierz pozycję **Ustawienia**, a następnie wyłącz weryfikację certyfikatu SSL.

---
  
> [!WARNING]
> Po przetestowaniu kontrolera ponownie Włącz weryfikację certyfikatu SSL.

* Utwórz nowe żądanie.
  * Ustaw metodę HTTP, aby **uzyskać**.
  * Ustaw identyfikator URI żądania na `https://localhost:<port>/api/todo` . Na przykład `https://localhost:5001/api/todo`.
* Ustaw **dwa widoki okienka** w programie Poster.
* Wybierz pozycję **Wyślij**.

![Ogłoś przy użyciu żądania GET](first-web-api/_static/2pv.png)

## <a name="add-a-create-method-21"></a>Dodawanie metody Create 2,1

Dodaj następującą `PostTodoItem` metodę w obszarze *controllers/TodoController. cs*: 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

Poprzedni kod jest metodą POST protokołu HTTP, jak wskazano w [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) atrybucie. Metoda pobiera wartość elementu do wykonania z treści żądania HTTP.

`CreatedAtAction`Metoda:

* Zwraca kod stanu HTTP 201, jeśli powodzenie. HTTP 201 to standardowa odpowiedź dla metody POST protokołu HTTP, która tworzy nowy zasób na serwerze.
* Dodaje `Location` nagłówek do odpowiedzi. `Location`Nagłówek określa identyfikator URI nowo utworzonego elementu do wykonania. Aby uzyskać więcej informacji, zobacz [10.2.2 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).
* Odwołuje się do `GetTodoItem` akcji tworzenia `Location` identyfikatora URI nagłówka. `nameof`Słowo kluczowe języka C# służy do zapobiegania twardemu kodowaniu nazwy akcji w `CreatedAtAction` wywołaniu.

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method-21"></a>Testowanie metody PostTodoItem 2,1

* Skompiluj projekt.
* W programie Poster ustaw metodę HTTP na `POST` .
* Ustaw identyfikator URI na `https://localhost:<port>/api/Todo` . Na przykład `https://localhost:5001/api/Todo`.
* Wybierz kartę **Treść**.
* Wybierz przycisk radiowy **RAW** .
* Ustaw typ na **JSON (Application/JSON)**.
* W treści żądania wprowadź kod JSON dla elementu do wykonania:

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* Wybierz pozycję **Wyślij**.

  ![Ogłoś przy użyciu żądania Create](first-web-api/_static/create.png)

  Jeśli wystąpi błąd 405 metody niedozwolonej, jest to prawdopodobnie wynik niekompilowania projektu po dodaniu `PostTodoItem` metody.

### <a name="test-the-location-header-uri-21"></a>Testowanie identyfikatora URI nagłówka lokalizacji 2,1

* Wybierz kartę **nagłówki** w okienku **odpowiedź** .
* Skopiuj wartość nagłówka **lokalizacji** :

  ![Karta nagłówki w konsoli programu Poster](first-web-api/_static/pmc2.png)

* Ustaw metodę, aby uzyskać.
* Ustaw identyfikator URI na `https://localhost:<port>/api/TodoItems/2` . Na przykład `https://localhost:5001/api/TodoItems/2`.
* Wybierz pozycję **Wyślij**.

## <a name="add-a-puttodoitem-method-21"></a>Dodawanie metody PutTodoItem 2,1

Dodaj następującą `PutTodoItem` metodę:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

`PutTodoItem` jest podobny do `PostTodoItem` , z tą różnicą, że używa protokołu HTTP Put. Odpowiedź to [204 (brak zawartości)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html). Zgodnie ze specyfikacją protokołu HTTP żądanie PUT wymaga, aby klient wysłał całą zaktualizowaną jednostkę, a nie tylko te zmiany. Aby zapewnić obsługę częściowych aktualizacji, użyj [poprawki http](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).

Jeśli wystąpi błąd podczas wywoływania `PutTodoItem` , wywołaj, `GET` Aby upewnić się, że w bazie danych znajduje się element.

### <a name="test-the-puttodoitem-method-21"></a>Testowanie metody PutTodoItem 2,1

Ten przykład korzysta z bazy danych w pamięci, która musi zostać zainicjowana za każdym razem, gdy aplikacja zostanie uruchomiona. Przed wykonaniem wywołania PUT musi istnieć element w bazie danych. Przed wykonaniem wywołania PUT należy wywołać metodę GET, aby upewnić się, że istnieje element w bazie danych.

Zaktualizuj element do wykonania o identyfikatorze 1 i ustaw jego nazwę na "Źródło danych":

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

Na poniższej ilustracji przedstawiono aktualizację programu Poster:

![Konsola programu Poster pokazująca odpowiedź 204 (brak zawartości)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method-21"></a>Dodawanie metody DeleteTodoItem 2,1

Dodaj następującą `DeleteTodoItem` metodę:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

`DeleteTodoItem`Odpowiedź to [204 (brak zawartości)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).

### <a name="test-the-deletetodoitem-method-21"></a>Testowanie metody DeleteTodoItem 2,1

Użyj programu Poster, aby usunąć element do wykonania:

* Ustaw metodę na `DELETE` .
* Ustaw identyfikator URI obiektu do usunięcia (na przykład `https://localhost:5001/api/todo/1` ).
* Wybierz pozycję **Wyślij**.

Przykładowa aplikacja umożliwia usunięcie wszystkich elementów. Jednak po usunięciu ostatniego elementu jest on tworzony przez konstruktora klasy modelu przy następnym wywołaniu interfejsu API.

## <a name="call-the-web-api-with-javascript-21"></a>Wywoływanie interfejsu API sieci Web przy użyciu języka JavaScript 2,1

W tej sekcji zostanie dodana strona HTML, która używa języka JavaScript do wywoływania internetowego interfejsu API. jQuery inicjuje żądanie. Język JavaScript aktualizuje stronę ze szczegółowymi informacjami z odpowiedzi internetowego interfejsu API.

Skonfiguruj aplikację do [obsługi plików statycznych](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) i [Włącz domyślne mapowanie plików](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) , aktualizując *Startup.cs* z następującym wyróżnionym kodem:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

Utwórz folder *wwwroot* w katalogu projektu.

Dodaj plik HTML o nazwie *index.html* do katalogu *wwwroot* . Zastąp jego zawartość następującym znacznikiem:

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

Dodaj plik języka JavaScript o nazwie *site.js* do katalogu *wwwroot* . Zastąp zawartość poniższym kodem:

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

Zmiana ustawień uruchamiania projektu ASP.NET Core może być wymagana do lokalnego przetestowania strony HTML:

* Otwórz *Properties\launchSettings.jsna*.
* Usuń `launchUrl` Właściwość, aby wymusić otwieranie przez aplikację w *index.html* &mdash; domyślnego pliku projektu.

Ten przykład wywołuje wszystkie metody CRUD internetowego interfejsu API. Poniżej znajdują się wyjaśnienia wywołań interfejsu API.

### <a name="get-a-list-of-to-do-items-21"></a>Pobierz listę elementów do wykonania 2,1

jQuery wysyła żądanie HTTP GET do internetowego interfejsu API, który zwraca kod JSON reprezentujący tablicę elementów do wykonania. `success`Funkcja wywołania zwrotnego jest wywoływana, jeśli żądanie zakończy się pomyślnie. W wywołaniu zwrotnym model DOM jest aktualizowany przy użyciu informacji o tym do wykonania.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item-21"></a>Dodaj element do wykonania 2,1

jQuery wysyła żądanie HTTP POST z elementem do wykonania w treści żądania. `accepts`Opcje i `contentType` są ustawione na, aby `application/json` określić typ nośnika odbierany i wysyłany. Element do wykonania jest konwertowany na format JSON przy użyciu [formatu JSON. stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify). Gdy interfejs API zwraca kod stanu pomyślnego, `getData` Funkcja jest wywoływana w celu zaktualizowania tabeli HTML.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item-21"></a>Aktualizowanie elementu do wykonania 2,1

Aktualizowanie elementu do wykonania jest podobne do dodawania jednego. `url`Zmiany mające na celu dodanie unikatowego identyfikatora elementu i `type` is `PUT` .

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item-21"></a>Usuń element do wykonania 2,1

Usuwanie elementu do wykonania jest realizowane przez ustawienie `type` w WYWOŁANIU AJAX `DELETE` i określenie unikatowego identyfikatora elementu w adresie URL.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api-21"></a>Dodawanie obsługi uwierzytelniania do internetowego interfejsu API 2,1

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources-21"></a>Dodatkowe zasoby 2,1

[Wyświetl lub Pobierz przykładowy kod dla tego samouczka](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples). Zobacz artykuł [jak pobrać](xref:index#how-to-download-a-sample).

Więcej informacji można znaleźć w następujących zasobach:

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [Wersja tego samouczka usługi YouTube](https://www.youtube.com/watch?v=TTkhEyGBfAk)
