---
title: Tworzenie internetowego interfejsu API za pomocą ASP.NET Core i MongoDB
author: prkhandelwal
description: W tym samouczku przedstawiono sposób tworzenia ASP.NET Core internetowego interfejsu API przy użyciu bazy danych NoSQL MongoDB.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 08/17/2019
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
uid: tutorials/first-mongo-app
ms.openlocfilehash: 12f08d953fa9050d8f15243371955644f8dbd149
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109497"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a>Tworzenie internetowego interfejsu API za pomocą ASP.NET Core i MongoDB

Autorzy [Pratik Khandelwal](https://twitter.com/K2Prk) i [Scott Addie](https://twitter.com/Scott_Addie)

::: moniker range=">= aspnetcore-3.0"

W tym samouczku przedstawiono Tworzenie interfejsu API sieci Web, który wykonuje operacje tworzenia, odczytu, aktualizacji i usuwania (CRUD) w bazie danych [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Konfigurowanie MongoDB
> * Tworzenie bazy danych MongoDB
> * Zdefiniuj kolekcję MongoDB i schemat
> * Wykonywanie operacji MongoDB CRUD z internetowego interfejsu API
> * Dostosowywanie serializacji JSON

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Zestaw .NET Core SDK 3.0 lub nowszy](https://dotnet.microsoft.com/download/dotnet-core)
* [Program Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) z **ASP.NET i programowaniem aplikacji sieci Web**
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Zestaw .NET Core SDK 3.0 lub nowszy](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio Code](https://code.visualstudio.com/download)
* [Rozszerzenie C# for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [MongoDB](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* [Zestaw .NET Core SDK 3.0 lub nowszy](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio dla komputerów Mac wersja 7,7 lub nowsza](https://visualstudio.microsoft.com/downloads/)
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a>Konfigurowanie MongoDB

W przypadku korzystania z systemu Windows MongoDB jest instalowany w *C: \\ Program Files \\ MongoDB* domyślnie. Dodaj *plik C: \\ Program Files \\ MongoDB \\ Server \\ \<version_number> \\ bin* do `Path` zmiennej środowiskowej. Ta zmiana umożliwia MongoDB dostęp z dowolnego miejsca na komputerze deweloperskim.

Użyj powłoki Mongo w poniższych krokach, aby utworzyć bazę danych, utworzyć kolekcje i przechowywać dokumenty. Aby uzyskać więcej informacji na temat poleceń powłoki Mongo, zobacz [Praca z powłoką Mongo](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).

1. Wybierz katalog na komputerze deweloperskim, który ma być używany do przechowywania danych. Na przykład *C: \\ BooksData* w systemie Windows. Utwórz katalog, jeśli nie istnieje. Powłoka Mongo nie tworzy nowych katalogów.
1. Otwórz powłokę poleceń. Uruchom następujące polecenie, aby nawiązać połączenie z usługą MongoDB na domyślnym porcie 27017. Pamiętaj, aby zamienić na `<data_directory_path>` katalog wybrany w poprzednim kroku.

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. Otwórz inne wystąpienie powłoki poleceń. Połącz się z domyślną bazą danych testów, uruchamiając następujące polecenie:

   ```console
   mongo
   ```

1. Uruchom następujące polecenie w powłoce poleceń:

   ```console
   use BookstoreDb
   ```

   Jeśli jeszcze nie istnieje, zostanie utworzona baza danych o nazwie *BookstoreDb* . Jeśli baza danych istnieje, jego połączenie jest otwierane dla transakcji.

1. Utwórz `Books` kolekcję przy użyciu następującego polecenia:

   ```console
   db.createCollection('Books')
   ```

   Zostanie wyświetlony następujący wynik:

   ```console
   { "ok" : 1 }
   ```

1. Zdefiniuj schemat `Books` kolekcji i Wstaw dwa dokumenty przy użyciu następującego polecenia:

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   Zostanie wyświetlony następujący wynik:

   ```console
   {
     "acknowledged" : true,
     "insertedIds" : [
       ObjectId("5bfd996f7b8e48dc15ff215d"),
       ObjectId("5bfd996f7b8e48dc15ff215e")
     ]
   }
   ```
  
   > [!NOTE]
   > Identyfikator przedstawiony w tym artykule nie będzie pasował do identyfikatorów podczas uruchamiania tego przykładu.

1. Wyświetl dokumenty w bazie danych przy użyciu następującego polecenia:

   ```console
   db.Books.find({}).pretty()
   ```

   Zostanie wyświetlony następujący wynik:

   ```console
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215d"),
     "Name" : "Design Patterns",
     "Price" : 54.93,
     "Category" : "Computers",
     "Author" : "Ralph Johnson"
   }
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215e"),
     "Name" : "Clean Code",
     "Price" : 43.15,
     "Category" : "Computers",
     "Author" : "Robert C. Martin"
   }
   ```

   Schemat dodaje automatycznie wygenerowaną `_id` Właściwość typu `ObjectId` dla każdego dokumentu.

Baza danych jest gotowa. Możesz rozpocząć tworzenie ASP.NET Core internetowego interfejsu API.

## <a name="create-the-aspnet-core-web-api-project"></a>Tworzenie projektu interfejsu API sieci Web ASP.NET Core

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Przejdź do pozycji **plik** > **Nowy** > **projekt**.
1. Wybierz typ projektu **aplikacja sieci Web ASP.NET Core** a następnie wybierz przycisk **dalej**.
1. Nazwij projekt *BooksApi*, a następnie wybierz pozycję **Utwórz**.
1. Wybierz platformę docelową **.NET Core** i **ASP.NET Core 3,0**. Wybierz szablon projektu **interfejsu API** i wybierz pozycję **Utwórz**.
1. Odwiedź [galerię NuGet: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) , aby określić najnowszą stabilną wersję sterownika .NET dla usługi MongoDB. W oknie **konsola Menedżera pakietów** przejdź do katalogu głównego projektu. Uruchom następujące polecenie, aby zainstalować sterownik .NET dla MongoDB:

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Uruchom następujące polecenia w powłoce poleceń:

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   Nowy projekt interfejsu API sieci Web ASP.NET Core przeznaczony dla platformy .NET Core został wygenerowany i otwarty w Visual Studio Code.

1. Gdy ikona płomienia OmniSharp na pasku stanu zmieni kolor na zielony, w oknie dialogowym zostanie wyświetlony monit **o podanie wymaganych zasobów do skompilowania i debugowania z elementu "BooksApi". Dodać je?**. Wybierz pozycję **Tak**.
1. Odwiedź [galerię NuGet: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) , aby określić najnowszą stabilną wersję sterownika .NET dla usługi MongoDB. Otwórz **zintegrowany terminal** i przejdź do katalogu głównego projektu. Uruchom następujące polecenie, aby zainstalować sterownik .NET dla MongoDB:

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

1. W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz pozycję **plik**  >  **nowe rozwiązanie**  >  **.NET Core**  >  **aplikacji** z paska bocznego. W wersji 8,6 lub nowszej wybierz pozycję **plik**  >  **nowe rozwiązanie**  >  **Sieć Web i Aplikacja konsolowa**  >   na pasku bocznym.
1. Wybierz  > szablon projektu C# **interfejsu API** ASP.NET Core, a następnie wybierz przycisk **dalej**.
1. Z listy rozwijanej **platforma docelowa** wybierz pozycję **.NET Core 3,1** , a następnie wybierz pozycję **Next (dalej**).
1. Wprowadź *BooksApi* jako **nazwę projektu**, a następnie wybierz pozycję **Utwórz**.
1. W konsoli **rozwiązania** kliknij prawym przyciskiem myszy węzeł **zależności** projektu i wybierz polecenie **Dodaj pakiety**.
1. Wprowadź *MongoDB. Driver* w polu wyszukiwania, wybierz pakiet *MongoDB. Driver* , a następnie wybierz pozycję **Dodaj pakiet**.
1. Wybierz przycisk **Akceptuj** w oknie dialogowym **akceptacji licencji** .

---

## <a name="add-an-entity-model"></a>Dodaj model jednostki

1. Dodaj katalog *models* do katalogu głównego projektu.
1. Dodaj `Book` klasę do katalogu *modeli* przy użyciu następującego kodu:

   ```csharp
   using MongoDB.Bson;
   using MongoDB.Bson.Serialization.Attributes;

   namespace BooksApi.Models
   {
       public class Book
       {
           [BsonId]
           [BsonRepresentation(BsonType.ObjectId)]
           public string Id { get; set; }

           [BsonElement("Name")]
           public string BookName { get; set; }

           public decimal Price { get; set; }

           public string Category { get; set; }

           public string Author { get; set; }
       }
   }
   ```

   W poprzedniej klasie `Id` Właściwość:

   * Jest wymagany do mapowania obiektu środowiska uruchomieniowego języka wspólnego (CLR) do kolekcji MongoDB.
   * Ma adnotację z, [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) Aby wyznaczyć tę właściwość jako klucz podstawowy dokumentu.
   * Ma adnotację z [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) , aby zezwolić na przekazywanie parametru jako typ `string` zamiast struktury [objectid](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) . Mongo obsługuje konwersję z `string` do `ObjectId` .

   `BookName`Właściwość ma adnotację z [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) atrybutem. Wartość atrybutu `Name` reprezentuje nazwę właściwości w kolekcji MongoDB.

## <a name="add-a-configuration-model"></a>Dodaj model konfiguracji

1. Dodaj następujące wartości konfiguracji bazy danych do *appsettings.json* :

   [!code-json[](first-mongo-app/samples/3.x/SampleApp/appsettings.json?highlight=2-6)]

1. Dodaj plik *BookstoreDatabaseSettings.cs* do katalogu *models* o następującym kodzie:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   Poprzednia `BookstoreDatabaseSettings` Klasa jest używana do przechowywania *appsettings.json* `BookstoreDatabaseSettings` wartości właściwości pliku. Nazwy właściwości JSON i C# są nazywane identycznie, aby uprościć proces mapowania.

1. Dodaj następujący wyróżniony kod do `Startup.ConfigureServices` :

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-8)]

   Powyższy kod ma następujące działanie:

   * Wystąpienie konfiguracji, do którego *appsettings.json* `BookstoreDatabaseSettings` jest powiązane sekcja pliku, jest zarejestrowane w kontenerze iniekcji zależności (di). Na przykład `BookstoreDatabaseSettings` `ConnectionString` właściwość obiektu jest wypełniana `BookstoreDatabaseSettings:ConnectionString` właściwością w *appsettings.json* .
   * `IBookstoreDatabaseSettings`Interfejs jest rejestrowany przy użyciu programu di z pojedynczym [okresem istnienia usługi](xref:fundamentals/dependency-injection#service-lifetimes). Po dowstrzykiwaniu wystąpienie interfejsu jest rozpoznawane jako `BookstoreDatabaseSettings` obiekt.

1. Dodaj następujący kod na początku *Startup.cs* , aby rozwiązać `BookstoreDatabaseSettings` `IBookstoreDatabaseSettings` odwołania i:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a>Dodawanie usługi operacji CRUD

1. Dodaj katalog *usług* do katalogu głównego projektu.
1. Dodaj `BookService` klasę do katalogu *usług* przy użyciu następującego kodu:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   W poprzednim kodzie `IBookstoreDatabaseSettings` wystąpienie jest pobierane z funkcji di przez iniekcję konstruktora. Ta technika zapewnia dostęp do *appsettings.json* wartości konfiguracyjnych, które zostały dodane w sekcji [Dodawanie modelu konfiguracji](#add-a-configuration-model) .

1. Dodaj następujący wyróżniony kod do `Startup.ConfigureServices` :

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   W poprzednim kodzie `BookService` Klasa jest zarejestrowana przy użyciu funkcji di, aby obsługiwać iniekcję konstruktora w klasach zużywających. Okres istnienia usługi pojedynczej jest najbardziej odpowiedni, ponieważ `BookService` Pobiera bezpośrednią zależność od `MongoClient` . Zgodnie z oficjalnymi [wskazówkami dotyczącymi ponownego użycia klienta Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use) `MongoClient` należy zarejestrować się w programie di z pojedynczym okresem istnienia usługi.

1. Dodaj następujący kod na początku *Startup.cs* , aby rozwiązać `BookService` odwołanie:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

`BookService`Klasa używa następujących `MongoDB.Driver` elementów członkowskich do wykonywania operacji CRUD w bazie danych:

* [MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): odczytuje wystąpienie serwera na potrzeby wykonywania operacji bazy danych. W konstruktorze tej klasy podano parametry połączenia MongoDB:

  [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* [IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): reprezentuje bazę danych Mongo do wykonywania operacji. W tym samouczku do uzyskiwania dostępu do danych w określonej kolekcji jest stosowana ogólna Metoda [getcollection \<TDocument> (Collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) . Wykonaj operacje CRUD w odniesieniu do kolekcji po wywołaniu tej metody. W `GetCollection<TDocument>(collection)` wywołaniu metody:

  * `collection` reprezentuje nazwę kolekcji.
  * `TDocument` reprezentuje typ obiektu CLR przechowywany w kolekcji.

`GetCollection<TDocument>(collection)` zwraca obiekt [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) reprezentujący kolekcję. W tym samouczku następujące metody są wywoływane w kolekcji:

* [DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Usuwa pojedynczy dokument pasujący do podanych kryteriów wyszukiwania.
* [Find \<TDocument> ](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): zwraca wszystkie dokumenty w kolekcji pasujące do podanych kryteriów wyszukiwania.
* [InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Wstawia podany obiekt jako nowy dokument w kolekcji.
* [ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): zamienia pojedynczy dokument pasujący do podanych kryteriów wyszukiwania z podanym obiektem.

## <a name="add-a-controller"></a>Dodawanie kontrolera

Dodaj `BooksController` klasę do katalogu *controllers* przy użyciu następującego kodu:

[!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Controllers/BooksController.cs)]

Poprzedni kontroler interfejsu API sieci Web:

* Używa `BookService` klasy do wykonywania operacji CRUD.
* Zawiera metody akcji do obsługi żądań HTTP GET, POST, PUT i DELETE.
* Wywołuje <xref:System.Web.Http.ApiController.CreatedAtRoute*> `Create` metodę akcji, aby zwrócić odpowiedź [http 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) . Kod stanu 201 jest standardową odpowiedzią dla metody POST protokołu HTTP, która tworzy nowy zasób na serwerze. `CreatedAtRoute` dodaje również `Location` nagłówek do odpowiedzi. `Location`Nagłówek określa identyfikator URI nowo utworzonej książki.

## <a name="test-the-web-api"></a>Testowanie interfejsu API sieci Web

1. Skompiluj i uruchom aplikację.

1. Przejdź do w `http://localhost:<port>/api/books` celu przetestowania metody akcji bez parametrów kontrolera `Get` . Zostanie wyświetlona następująca odpowiedź JSON:

   ```json
   [
     {
       "id":"5bfd996f7b8e48dc15ff215d",
       "bookName":"Design Patterns",
       "price":54.93,
       "category":"Computers",
       "author":"Ralph Johnson"
     },
     {
       "id":"5bfd996f7b8e48dc15ff215e",
       "bookName":"Clean Code",
       "price":43.15,
       "category":"Computers",
       "author":"Robert C. Martin"
     }
   ]
   ```

1. Przejdź do `http://localhost:<port>/api/books/{id here}` , aby przetestować przeciążoną `Get` metodę działania kontrolera. Zostanie wyświetlona następująca odpowiedź JSON:

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a>Konfigurowanie opcji serializacji JSON

Istnieją dwa szczegóły dotyczące odpowiedzi JSON zwróconych w sekcji [testowanie interfejsu API sieci Web](#test-the-web-api) :

* Nazwy właściwości "default notacji CamelCase wielkość liter należy zmienić tak, aby pasowały do wielkości liter w języku Pascal nazw właściwości obiektu CLR.
* `bookName`Właściwość powinna zostać zwrócona jako `Name` .

Aby spełnić powyższe wymagania, należy wprowadzić następujące zmiany:

1. JSON.NET został usunięty z ASP.NET udostępnionej platformy. Dodaj odwołanie do pakietu do [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson) .

1. W programie `Startup.ConfigureServices` łańcuchować następujący wyróżniony kod w `AddControllers` wywołaniu metody:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   W przypadku poprzedniej zmiany nazwy właściwości w serializowanej odpowiedzi JSON interfejsu API sieci Web pasują do odpowiednich nazw właściwości w typie obiektu CLR. Na przykład, `Book` `Author` Serializacja właściwości klasy jako `Author` .

1. W *modelach/książka. cs* Dodaj adnotację do `BookName` właściwości z następującym [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) atrybutem:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   `[JsonProperty]`Wartość atrybutu `Name` reprezentuje nazwę właściwości w serializowanej odpowiedzi JSON interfejsu API sieci Web.

1. Dodaj następujący kod na górze *modeli/książek. cs* , aby rozwiązać `[JsonProperty]` odwołanie do atrybutu:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. Powtórz kroki zdefiniowane w sekcji [testowanie interfejsu API sieci Web](#test-the-web-api) . Zwróć uwagę na różnice w nazwach właściwości JSON.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

W tym samouczku przedstawiono Tworzenie interfejsu API sieci Web, który wykonuje operacje tworzenia, odczytu, aktualizacji i usuwania (CRUD) w bazie danych [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Konfigurowanie MongoDB
> * Tworzenie bazy danych MongoDB
> * Zdefiniuj kolekcję MongoDB i schemat
> * Wykonywanie operacji MongoDB CRUD z internetowego interfejsu API
> * Dostosowywanie serializacji JSON

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Zestaw .NET Core SDK 2,2](https://dotnet.microsoft.com/download/dotnet-core)
* [Program Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) z **ASP.NET i programowaniem aplikacji sieci Web**
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Zestaw .NET Core SDK 2,2](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio Code](https://code.visualstudio.com/download)
* [Rozszerzenie C# for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [MongoDB](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* [Zestaw .NET Core SDK 2,2](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio dla komputerów Mac wersja 7,7 lub nowsza](https://visualstudio.microsoft.com/downloads/)
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a>Konfigurowanie MongoDB

W przypadku korzystania z systemu Windows MongoDB jest instalowany w *C: \\ Program Files \\ MongoDB* domyślnie. Dodaj *plik C: \\ Program Files \\ MongoDB \\ Server \\ \<version_number> \\ bin* do `Path` zmiennej środowiskowej. Ta zmiana umożliwia MongoDB dostęp z dowolnego miejsca na komputerze deweloperskim.

Użyj powłoki Mongo w poniższych krokach, aby utworzyć bazę danych, utworzyć kolekcje i przechowywać dokumenty. Aby uzyskać więcej informacji na temat poleceń powłoki Mongo, zobacz [Praca z powłoką Mongo](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).

1. Wybierz katalog na komputerze deweloperskim, który ma być używany do przechowywania danych. Na przykład *C: \\ BooksData* w systemie Windows. Utwórz katalog, jeśli nie istnieje. Powłoka Mongo nie tworzy nowych katalogów.
1. Otwórz powłokę poleceń. Uruchom następujące polecenie, aby nawiązać połączenie z usługą MongoDB na domyślnym porcie 27017. Pamiętaj, aby zamienić na `<data_directory_path>` katalog wybrany w poprzednim kroku.

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. Otwórz inne wystąpienie powłoki poleceń. Połącz się z domyślną bazą danych testów, uruchamiając następujące polecenie:

   ```console
   mongo
   ```

1. Uruchom następujące polecenie w powłoce poleceń:

   ```console
   use BookstoreDb
   ```

   Jeśli jeszcze nie istnieje, zostanie utworzona baza danych o nazwie *BookstoreDb* . Jeśli baza danych istnieje, jego połączenie jest otwierane dla transakcji.

1. Utwórz `Books` kolekcję przy użyciu następującego polecenia:

   ```console
   db.createCollection('Books')
   ```

   Zostanie wyświetlony następujący wynik:

   ```console
   { "ok" : 1 }
   ```

1. Zdefiniuj schemat `Books` kolekcji i Wstaw dwa dokumenty przy użyciu następującego polecenia:

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   Zostanie wyświetlony następujący wynik:

   ```console
   {
     "acknowledged" : true,
     "insertedIds" : [
       ObjectId("5bfd996f7b8e48dc15ff215d"),
       ObjectId("5bfd996f7b8e48dc15ff215e")
     ]
   }
   ```
  
   > [!NOTE]
   > Identyfikator przedstawiony w tym artykule nie będzie pasował do identyfikatorów podczas uruchamiania tego przykładu.

1. Wyświetl dokumenty w bazie danych przy użyciu następującego polecenia:

   ```console
   db.Books.find({}).pretty()
   ```

   Zostanie wyświetlony następujący wynik:

   ```console
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215d"),
     "Name" : "Design Patterns",
     "Price" : 54.93,
     "Category" : "Computers",
     "Author" : "Ralph Johnson"
   }
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215e"),
     "Name" : "Clean Code",
     "Price" : 43.15,
     "Category" : "Computers",
     "Author" : "Robert C. Martin"
   }
   ```

   Schemat dodaje automatycznie wygenerowaną `_id` Właściwość typu `ObjectId` dla każdego dokumentu.

Baza danych jest gotowa. Możesz rozpocząć tworzenie ASP.NET Core internetowego interfejsu API.

## <a name="create-the-aspnet-core-web-api-project"></a>Tworzenie projektu interfejsu API sieci Web ASP.NET Core

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Przejdź do pozycji **plik** > **Nowy** > **projekt**.
1. Wybierz typ projektu **aplikacja sieci Web ASP.NET Core** a następnie wybierz przycisk **dalej**.
1. Nazwij projekt *BooksApi*, a następnie wybierz pozycję **Utwórz**.
1. Wybierz platformę docelową **.NET Core** i **ASP.NET Core 2,2**. Wybierz szablon projektu **interfejsu API** i wybierz pozycję **Utwórz**.
1. Odwiedź [galerię NuGet: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) , aby określić najnowszą stabilną wersję sterownika .NET dla usługi MongoDB. W oknie **konsola Menedżera pakietów** przejdź do katalogu głównego projektu. Uruchom następujące polecenie, aby zainstalować sterownik .NET dla MongoDB:

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Uruchom następujące polecenia w powłoce poleceń:

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   Nowy projekt interfejsu API sieci Web ASP.NET Core przeznaczony dla platformy .NET Core został wygenerowany i otwarty w Visual Studio Code.

1. Gdy ikona płomienia OmniSharp na pasku stanu zmieni kolor na zielony, w oknie dialogowym zostanie wyświetlony monit **o podanie wymaganych zasobów do skompilowania i debugowania z elementu "BooksApi". Dodać je?**. Wybierz pozycję **Tak**.
1. Odwiedź [galerię NuGet: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) , aby określić najnowszą stabilną wersję sterownika .NET dla usługi MongoDB. Otwórz **zintegrowany terminal** i przejdź do katalogu głównego projektu. Uruchom następujące polecenie, aby zainstalować sterownik .NET dla MongoDB:

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

1. W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz pozycję **plik**  >  **nowe rozwiązanie**  >  **.NET Core**  >  **aplikacji** z paska bocznego. W wersji 8,6 lub nowszej wybierz pozycję **plik**  >  **nowe rozwiązanie**  >  **Sieć Web i Aplikacja konsolowa**  >   na pasku bocznym.
1. Wybierz szablon projektu C# **interfejsu API sieci Web ASP.NET Core** i kliknij przycisk **dalej**.
1. Z listy rozwijanej **platforma docelowa** wybierz pozycję **.NET Core 2,2** , a następnie wybierz pozycję **Next (dalej**).
1. Wprowadź *BooksApi* jako **nazwę projektu**, a następnie wybierz pozycję **Utwórz**.
1. W konsoli **rozwiązania** kliknij prawym przyciskiem myszy węzeł **zależności** projektu i wybierz polecenie **Dodaj pakiety**.
1. Wprowadź *MongoDB. Driver* w polu wyszukiwania, wybierz pakiet *MongoDB. Driver* , a następnie wybierz pozycję **Dodaj pakiet**.
1. Wybierz przycisk **Akceptuj** w oknie dialogowym **akceptacji licencji** .

---

## <a name="add-an-entity-model"></a>Dodaj model jednostki

1. Dodaj katalog *models* do katalogu głównego projektu.
1. Dodaj `Book` klasę do katalogu *modeli* przy użyciu następującego kodu:

   ```csharp
   using MongoDB.Bson;
   using MongoDB.Bson.Serialization.Attributes;

   namespace BooksApi.Models
   {
       public class Book
       {
           [BsonId]
           [BsonRepresentation(BsonType.ObjectId)]
           public string Id { get; set; }

           [BsonElement("Name")]
           public string BookName { get; set; }

           public decimal Price { get; set; }

           public string Category { get; set; }

           public string Author { get; set; }
       }
   }
   ```

   W poprzedniej klasie `Id` Właściwość:

   * Jest wymagany do mapowania obiektu środowiska uruchomieniowego języka wspólnego (CLR) do kolekcji MongoDB.
   * Ma adnotację z, [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) Aby wyznaczyć tę właściwość jako klucz podstawowy dokumentu.
   * Ma adnotację z [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) , aby zezwolić na przekazywanie parametru jako typ `string` zamiast struktury [objectid](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) . Mongo obsługuje konwersję z `string` do `ObjectId` .

   `BookName`Właściwość ma adnotację z [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) atrybutem. Wartość atrybutu `Name` reprezentuje nazwę właściwości w kolekcji MongoDB.

## <a name="add-a-configuration-model"></a>Dodaj model konfiguracji

1. Dodaj następujące wartości konfiguracji bazy danych do *appsettings.json* :

   [!code-json[](first-mongo-app/samples/2.x/SampleApp/appsettings.json?highlight=2-6)]

1. Dodaj plik *BookstoreDatabaseSettings.cs* do katalogu *models* o następującym kodzie:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   Poprzednia `BookstoreDatabaseSettings` Klasa jest używana do przechowywania *appsettings.json* `BookstoreDatabaseSettings` wartości właściwości pliku. Nazwy właściwości JSON i C# są nazywane identycznie, aby uprościć proces mapowania.

1. Dodaj następujący wyróżniony kod do `Startup.ConfigureServices` :

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   Powyższy kod ma następujące działanie:

   * Wystąpienie konfiguracji, do którego *appsettings.json* `BookstoreDatabaseSettings` jest powiązane sekcja pliku, jest zarejestrowane w kontenerze iniekcji zależności (di). Na przykład `BookstoreDatabaseSettings` `ConnectionString` właściwość obiektu jest wypełniana `BookstoreDatabaseSettings:ConnectionString` właściwością w *appsettings.json* .
   * `IBookstoreDatabaseSettings`Interfejs jest rejestrowany przy użyciu programu di z pojedynczym [okresem istnienia usługi](xref:fundamentals/dependency-injection#service-lifetimes). Po dowstrzykiwaniu wystąpienie interfejsu jest rozpoznawane jako `BookstoreDatabaseSettings` obiekt.

1. Dodaj następujący kod na początku *Startup.cs* , aby rozwiązać `BookstoreDatabaseSettings` `IBookstoreDatabaseSettings` odwołania i:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a>Dodawanie usługi operacji CRUD

1. Dodaj katalog *usług* do katalogu głównego projektu.
1. Dodaj `BookService` klasę do katalogu *usług* przy użyciu następującego kodu:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   W poprzednim kodzie `IBookstoreDatabaseSettings` wystąpienie jest pobierane z funkcji di przez iniekcję konstruktora. Ta technika zapewnia dostęp do *appsettings.json* wartości konfiguracyjnych, które zostały dodane w sekcji [Dodawanie modelu konfiguracji](#add-a-configuration-model) .

1. Dodaj następujący wyróżniony kod do `Startup.ConfigureServices` :

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   W poprzednim kodzie `BookService` Klasa jest zarejestrowana przy użyciu funkcji di, aby obsługiwać iniekcję konstruktora w klasach zużywających. Okres istnienia usługi pojedynczej jest najbardziej odpowiedni, ponieważ `BookService` Pobiera bezpośrednią zależność od `MongoClient` . Zgodnie z oficjalnymi [wskazówkami dotyczącymi ponownego użycia klienta Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use) `MongoClient` należy zarejestrować się w programie di z pojedynczym okresem istnienia usługi.

1. Dodaj następujący kod na początku *Startup.cs* , aby rozwiązać `BookService` odwołanie:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

`BookService`Klasa używa następujących `MongoDB.Driver` elementów członkowskich do wykonywania operacji CRUD w bazie danych:

* [MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): odczytuje wystąpienie serwera na potrzeby wykonywania operacji bazy danych. W konstruktorze tej klasy podano parametry połączenia MongoDB:

  [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* [IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): reprezentuje bazę danych Mongo do wykonywania operacji. W tym samouczku do uzyskiwania dostępu do danych w określonej kolekcji jest stosowana ogólna Metoda [getcollection \<TDocument> (Collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) . Wykonaj operacje CRUD w odniesieniu do kolekcji po wywołaniu tej metody. W `GetCollection<TDocument>(collection)` wywołaniu metody:

  * `collection` reprezentuje nazwę kolekcji.
  * `TDocument` reprezentuje typ obiektu CLR przechowywany w kolekcji.

`GetCollection<TDocument>(collection)` zwraca obiekt [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) reprezentujący kolekcję. W tym samouczku następujące metody są wywoływane w kolekcji:

* [DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Usuwa pojedynczy dokument pasujący do podanych kryteriów wyszukiwania.
* [Find \<TDocument> ](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): zwraca wszystkie dokumenty w kolekcji pasujące do podanych kryteriów wyszukiwania.
* [InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Wstawia podany obiekt jako nowy dokument w kolekcji.
* [ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): zamienia pojedynczy dokument pasujący do podanych kryteriów wyszukiwania z podanym obiektem.

## <a name="add-a-controller"></a>Dodawanie kontrolera

Dodaj `BooksController` klasę do katalogu *controllers* przy użyciu następującego kodu:

[!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Controllers/BooksController.cs)]

Poprzedni kontroler interfejsu API sieci Web:

* Używa `BookService` klasy do wykonywania operacji CRUD.
* Zawiera metody akcji do obsługi żądań HTTP GET, POST, PUT i DELETE.
* Wywołuje <xref:System.Web.Http.ApiController.CreatedAtRoute*> `Create` metodę akcji, aby zwrócić odpowiedź [http 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) . Kod stanu 201 jest standardową odpowiedzią dla metody POST protokołu HTTP, która tworzy nowy zasób na serwerze. `CreatedAtRoute` dodaje również `Location` nagłówek do odpowiedzi. `Location`Nagłówek określa identyfikator URI nowo utworzonej książki.

## <a name="test-the-web-api"></a>Testowanie interfejsu API sieci Web

1. Skompiluj i uruchom aplikację.

1. Przejdź do w `http://localhost:<port>/api/books` celu przetestowania metody akcji bez parametrów kontrolera `Get` . Zostanie wyświetlona następująca odpowiedź JSON:

   ```json
   [
     {
       "id":"5bfd996f7b8e48dc15ff215d",
       "bookName":"Design Patterns",
       "price":54.93,
       "category":"Computers",
       "author":"Ralph Johnson"
     },
     {
       "id":"5bfd996f7b8e48dc15ff215e",
       "bookName":"Clean Code",
       "price":43.15,
       "category":"Computers",
       "author":"Robert C. Martin"
     }
   ]
   ```

1. Przejdź do `http://localhost:<port>/api/books/{id here}` , aby przetestować przeciążoną `Get` metodę działania kontrolera. Zostanie wyświetlona następująca odpowiedź JSON:

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a>Konfigurowanie opcji serializacji JSON

Istnieją dwa szczegóły dotyczące odpowiedzi JSON zwróconych w sekcji [testowanie interfejsu API sieci Web](#test-the-web-api) :

* Nazwy właściwości "default notacji CamelCase wielkość liter należy zmienić tak, aby pasowały do wielkości liter w języku Pascal nazw właściwości obiektu CLR.
* `bookName`Właściwość powinna zostać zwrócona jako `Name` .

Aby spełnić powyższe wymagania, należy wprowadzić następujące zmiany:

1. W programie `Startup.ConfigureServices` łańcuchować następujący wyróżniony kod w `AddMvc` wywołaniu metody:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   W przypadku poprzedniej zmiany nazwy właściwości w serializowanej odpowiedzi JSON interfejsu API sieci Web pasują do odpowiednich nazw właściwości w typie obiektu CLR. Na przykład, `Book` `Author` Serializacja właściwości klasy jako `Author` .

1. W *modelach/książka. cs* Dodaj adnotację do `BookName` właściwości z następującym [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) atrybutem:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   `[JsonProperty]`Wartość atrybutu `Name` reprezentuje nazwę właściwości w serializowanej odpowiedzi JSON interfejsu API sieci Web.

1. Dodaj następujący kod na górze *modeli/książek. cs* , aby rozwiązać `[JsonProperty]` odwołanie do atrybutu:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. Powtórz kroki zdefiniowane w sekcji [testowanie interfejsu API sieci Web](#test-the-web-api) . Zwróć uwagę na różnice w nazwach właściwości JSON.

::: moniker-end

## <a name="add-authentication-support-to-a-web-api"></a>Dodawanie obsługi uwierzytelniania do internetowego interfejsu API

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat tworzenia ASP.NET Core interfejsów API sieci Web, zobacz następujące zasoby:

* [Wersja tego artykułu usługi YouTube](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
* [Microsoft Learn: Tworzenie interfejsu API sieci Web za pomocą ASP.NET Core](/learn/modules/build-web-api-aspnet-core/)
