---
title: Tworzenie internetowego interfejsu API z ASP.NET Core i MongoDB
author: prkhandelwal
description: W tym samouczku pokazano, jak utworzyć ASP.NET core internetowego interfejsu API przy użyciu bazy danych MongoDB NoSQL.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 08/17/2019
uid: tutorials/first-mongo-app
ms.openlocfilehash: d5ce4a1dc3c00b2b12edc12e26f482caa97df6b3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511421"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a>Tworzenie internetowego interfejsu API z ASP.NET Core i MongoDB

Przez [Pratik Khandelwal](https://twitter.com/K2Prk) i [Scott Addie](https://twitter.com/Scott_Addie)

::: moniker range=">= aspnetcore-3.0"

W tym samouczku utworzy się internetowy interfejs API, który wykonuje operacje tworzenia, odczytu, aktualizacji i usuwania (CRUD) w bazie danych [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie bazy danych MongoDB
> * Tworzenie bazy danych MongoDB
> * Definiowanie kolekcji i schematu mongodb
> * Wykonywanie operacji MONGODB CRUD z internetowego interfejsu API
> * Dostosowywanie serializacji JSON

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* [Zestaw .NET Core SDK 3.0 lub nowszy](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) z **obciążeniem ASP.NET i tworzenia stron internetowych**
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Zestaw .NET Core SDK 3.0 lub nowszy](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio Code](https://code.visualstudio.com/download)
* [Rozszerzenie C# for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [MongoDB](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* [Zestaw .NET Core SDK 3.0 lub nowszy](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio dla komputerów Mac w wersji 7.7 lub nowszej](https://visualstudio.microsoft.com/downloads/)
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a>Konfigurowanie bazy danych MongoDB

Jeśli używasz systemu Windows, MongoDB jest zainstalowany w *C:\\Program Files\\MongoDB* domyślnie. Dodaj *C:\\\\Pliki programów MongoDB\\Server\\\<version_number \\>* `Path` pojemnik do zmiennej środowiskowej. Ta zmiana umożliwia dostęp MongoDB z dowolnego miejsca na komputerze deweloperskim.

Użyj powłoki mongo w poniższych krokach, aby utworzyć bazę danych, utworzyć kolekcje i przechowywać dokumenty. Aby uzyskać więcej informacji na temat poleceń mongo Shell, zobacz [Praca z powłoką mongo](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).

1. Wybierz katalog na komputerze deweloperskim do przechowywania danych. Na przykład *C:\\BooksData* w systemie Windows. Utwórz katalog, jeśli nie istnieje. Mongo Shell nie tworzy nowych katalogów.
1. Otwórz powłokę polecenia. Uruchom następujące polecenie, aby połączyć się z mongodb na domyślnym porcie 27017. Pamiętaj, `<data_directory_path>` aby zastąpić katalog wybrany w poprzednim kroku.

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. Otwórz inne wystąpienie powłoki polecenia. Połącz się z domyślną bazą danych testów, uruchamiając następujące polecenie:

   ```console
   mongo
   ```

1. Uruchom następujące polecenie w powłoce polecenia:

   ```console
   use BookstoreDb
   ```

   Jeśli jeszcze nie istnieje, tworzona jest baza danych o nazwie *BookstoreDb.* Jeśli baza danych istnieje, jej połączenie jest otwierane dla transakcji.

1. Utwórz `Books` kolekcję za pomocą następującego polecenia:

   ```console
   db.createCollection('Books')
   ```

   Wyświetlany jest następujący wynik:

   ```console
   { "ok" : 1 }
   ```

1. Zdefiniuj `Books` schemat kolekcji i wstaw dwa dokumenty za pomocą następującego polecenia:

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   Wyświetlany jest następujący wynik:

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
   > Identyfikator jest wyświetlany w tym artykule nie będzie zgodne identyfikatory po uruchomieniu tego przykładu.

1. Wyświetlanie dokumentów w bazie danych za pomocą następującego polecenia:

   ```console
   db.Books.find({}).pretty()
   ```

   Wyświetlany jest następujący wynik:

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

   Schemat dodaje autoryzowaną `_id` właściwość `ObjectId` typu dla każdego dokumentu.

Baza danych jest gotowa. Można rozpocząć tworzenie ASP.NET Core interfejsu API sieci web.

## <a name="create-the-aspnet-core-web-api-project"></a>Tworzenie projektu internetowego interfejsu API ASP.NET Core

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

1. Przejdź do **pliku** > **nowego** > **projektu**.
1. Wybierz typ projektu **ASP.NET Core Web Application** i wybierz przycisk **Dalej**.
1. Nazwij projekt *BooksApi*i wybierz pozycję **Utwórz**.
1. Wybierz platformę docelową **.NET Core** i **ASP.NET Core 3.0**. Wybierz szablon projektu **interfejsu API** i wybierz pozycję **Utwórz**.
1. Odwiedź [Galerię NuGet: MongoDB.Driver,](https://www.nuget.org/packages/MongoDB.Driver/) aby określić najnowszą stabilną wersję sterownika .NET dla mongodb. W oknie **Konsoli Menedżera pakietów** przejdź do katalogu głównego projektu. Uruchom następujące polecenie, aby zainstalować sterownik .NET dla usługi MongoDB:

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Uruchom następujące polecenia w powłoce polecenia:

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   Nowy projekt interfejsu API sieci web ASP.NET Core jest generowany i otwierany w programie Visual Studio Code.

1. Po pasku stanu OmniSharp płomień ikona zmieni kolor na zielony, okno dialogowe pyta **wymagane zasoby do tworzenia i debugowania brakuje "BooksApi". Dodać je?**. Wybierz **pozycję Tak**.
1. Odwiedź [Galerię NuGet: MongoDB.Driver,](https://www.nuget.org/packages/MongoDB.Driver/) aby określić najnowszą stabilną wersję sterownika .NET dla mongodb. Otwórz **zintegrowany terminal** i przejdź do katalogu głównego projektu. Uruchom następujące polecenie, aby zainstalować sterownik .NET dla usługi MongoDB:

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

1. Przejdź do **pliku** > **nowego rozwiązania** > **.NET Core** > **App**.
1. Wybierz szablon projektu **ASP.NET Core Web API** C# i wybierz pozycję **Dalej**.
1. Z listy rozwijanej **Rama docelowa** wybierz pozycję **.NET Core 3.0,** a następnie wybierz pozycję **Dalej**.
1. Wprowadź *pozycję BooksApi* dla **nazwy projektu**i wybierz pozycję **Utwórz**.
1. W konsoli **Rozwiązania** kliknij prawym przyciskiem myszy węzeł **Zależności** projektu i wybierz polecenie **Dodaj pakiety**.
1. Wprowadź *mongoDB.driver* w polu wyszukiwania, wybierz pakiet *MongoDB.Driver* i wybierz **dodaj pakiet**.
1. Wybierz przycisk **Zaakceptuj** w oknie dialogowym **Akceptacja licencji.**

---

## <a name="add-an-entity-model"></a>Dodawanie modelu encji

1. Dodaj katalog *Models* do katalogu głównego projektu.
1. Dodaj `Book` klasę do katalogu *Models* z następującym kodem:

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

   W poprzedniej klasie `Id` właściwość:

   * Jest wymagane do mapowania obiektu środowiska wykonawczego języka wspólnego (CLR) do kolekcji MongoDB.
   * Jest oś [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) tym adnotacjami, aby wyznaczyć tę właściwość jako klucz podstawowy dokumentu.
   * Jest opisywany [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) z aby umożliwić przekazywanie parametru jako typu `string` zamiast [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) struktury. Mongo obsługuje konwersję `string` `ObjectId`z do .

   Właściwość `BookName` jest opisywana [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) z atrybutem. Wartość atrybutu `Name` reprezentuje nazwę właściwości w kolekcji MongoDB.

## <a name="add-a-configuration-model"></a>Dodawanie modelu konfiguracji

1. Dodaj następujące wartości konfiguracji bazy danych do *pliku appsettings.json:*

   [!code-json[](first-mongo-app/samples/3.x/SampleApp/appsettings.json?highlight=2-6)]

1. Dodaj plik *BookstoreDatabaseSettings.cs* do katalogu *Models* z następującym kodem:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   Poprzednia `BookstoreDatabaseSettings` klasa jest używana do przechowywania wartości `BookstoreDatabaseSettings` właściwości pliku *appsettings.json.* Nazwy właściwości JSON i C# są nazwane identycznie, aby ułatwić proces mapowania.

1. Dodaj następujący wyróżniony `Startup.ConfigureServices`kod do:

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-8)]

   Powyższy kod ma następujące działanie:

   * Wystąpienie konfiguracji, z którym wiąże `BookstoreDatabaseSettings` się sekcja pliku *appsettings.json,* jest zarejestrowane w kontenerze iniekcji zależności (DI). Na przykład `BookstoreDatabaseSettings` właściwość `ConnectionString` obiektu jest wypełniona właściwością `BookstoreDatabaseSettings:ConnectionString` w *pliku appsettings.json*.
   * Interfejs `IBookstoreDatabaseSettings` jest zarejestrowany w DI z [okresu istnienia usługi](xref:fundamentals/dependency-injection#service-lifetimes)singleton . Po wstrzyknięciu wystąpienie interfejsu jest `BookstoreDatabaseSettings` rozpoznawane dla obiektu.

1. Dodaj następujący kod do górnej części *Startup.cs,* aby rozwiązać `BookstoreDatabaseSettings` i `IBookstoreDatabaseSettings` odwołania:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a>Dodawanie usługi operacji CRUD

1. Dodaj katalog *usług* do katalogu głównego projektu.
1. Dodaj `BookService` klasę do katalogu *usług* z następującym kodem:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   W poprzednim kodzie `IBookstoreDatabaseSettings` wystąpienie jest pobierane z DI za pośrednictwem iniekcji konstruktora. Ta technika zapewnia dostęp do wartości konfiguracji *appsettings.json,* które zostały dodane w sekcji [Dodaj model konfiguracji.](#add-a-configuration-model)

1. Dodaj następujący wyróżniony `Startup.ConfigureServices`kod do:

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   W poprzednim kodzie `BookService` klasa jest zarejestrowana w DI do obsługi iniekcji konstruktora w klasach zużywających. Okres istnienia usługi singleton `BookService` jest najbardziej odpowiedni, `MongoClient`ponieważ przyjmuje bezpośrednią zależność od . Zgodnie z [oficjalnymi wytycznymi mongo klienta ponownego wykorzystania,](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use) `MongoClient` powinny być zarejestrowane w DI z okresu istnienia usługi singleton.

1. Dodaj następujący kod do górnej części *Startup.cs,* aby rozwiązać `BookService` odwołanie:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

Klasa `BookService` używa następujących `MongoDB.Driver` elementów członkowskich do wykonywania operacji CRUD w bazie danych:

* [MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; odczytuje wystąpienie serwera do wykonywania operacji bazy danych. Konstruktor tej klasy jest pod warunkiem parametry połączenia MongoDB:

  [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* [IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Reprezentuje mongo bazy danych do wykonywania operacji. W tym samouczku użyto ogólnej metody [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) w interfejsie, aby uzyskać dostęp do danych w określonej kolekcji. Wykonaj operacje CRUD względem kolekcji po wywołaniu tej metody. W `GetCollection<TDocument>(collection)` wywołaniu metody:

  * `collection`reprezentuje nazwę kolekcji.
  * `TDocument`reprezentuje typ obiektu CLR przechowywane w kolekcji.

`GetCollection<TDocument>(collection)`zwraca [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) obiektu reprezentującego kolekcję. W tym samouczku są wywoływane w kolekcji następujące metody:

* [DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Usuwa pojedynczy dokument spełniający podane kryteria wyszukiwania.
* [Znajdź\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Zwraca wszystkie dokumenty w kolekcji zgodne z podanymi kryteriami wyszukiwania.
* [InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Wstawia podany obiekt jako nowy dokument w kolekcji.
* [ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Zastępuje pojedynczy dokument pasujący do podanych kryteriów wyszukiwania z podanym obiektem.

## <a name="add-a-controller"></a>Dodawanie kontrolera

Dodaj `BooksController` klasę do katalogu *Controllers* z następującym kodem:

[!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Controllers/BooksController.cs)]

Poprzedni kontroler interfejsu API sieci web:

* Używa `BookService` klasy do wykonywania operacji CRUD.
* Zawiera metody akcji do obsługi żądań HTTP GET, POST, PUT i DELETE.
* Wywołania <xref:System.Web.Http.ApiController.CreatedAtRoute*> `Create` w metodzie akcji, aby zwrócić odpowiedź [HTTP 201.](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) Kod stanu 201 jest standardową odpowiedzią dla metody HTTP POST, która tworzy nowy zasób na serwerze. `CreatedAtRoute`również dodaje `Location` nagłówek do odpowiedzi. Nagłówek `Location` określa identyfikator URI nowo utworzonej książki.

## <a name="test-the-web-api"></a>Testowanie internetowego interfejsu API

1. Skompiluj i uruchom aplikację.

1. Przejdź `http://localhost:<port>/api/books` do, aby przetestować `Get` metodę akcji bez parametrów kontrolera. Wyświetlana jest następująca odpowiedź JSON:

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

1. Przejdź `http://localhost:<port>/api/books/{id here}` do, aby przetestować metodę `Get` akcji przeciążone kontrolera. Wyświetlana jest następująca odpowiedź JSON:

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

Istnieją dwa szczegóły do zmiany dotyczące odpowiedzi JSON zwróconych w sekcji [Testowanie interfejsu API sieci web:](#test-the-web-api)

* Domyślna wielkość liter na wielbłądzie nazw właściwości powinna zostać zmieniona tak, aby odpowiadała wielkości liter pascalowej nazw właściwości obiektu CLR.
* Właściwość `bookName` powinna zostać `Name`zwrócona jako .

Aby spełnić powyższe wymagania, należy wprowadzić następujące zmiany:

1. JSON.NET został usunięty z ASP.NET udostępnionych ram. Dodaj odwołanie do pakietu [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).

1. W `Startup.ConfigureServices`, łańcuch następujący wyróżniony `AddControllers` kod na wywołanie metody:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   Wraz z poprzednią zmianą nazwy właściwości w serializowanej odpowiedzi JSON interfejsu API sieci web są zgodne z odpowiadającymi im nazwami właściwości w typie obiektu CLR. Na przykład `Book` `Author` właściwość klasy serializuje `Author`się jako .

1. W *models/book.cs*, adnotacje `BookName` właściwości [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) z następującym atrybutem:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   Wartość `[JsonProperty]` atrybutu `Name` reprezentuje nazwę właściwości w serializowanej odpowiedzi JSON interfejsu API sieci web.

1. Dodaj następujący kod do góry *Models/Book.cs,* aby rozwiązać odwołanie do atrybutu: `[JsonProperty]`

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. Powtórz kroki zdefiniowane w sekcji [Testowanie internetowego interfejsu API.](#test-the-web-api) Zwróć uwagę na różnicę w nazwach właściwości JSON.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

W tym samouczku utworzy się internetowy interfejs API, który wykonuje operacje tworzenia, odczytu, aktualizacji i usuwania (CRUD) w bazie danych [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie bazy danych MongoDB
> * Tworzenie bazy danych MongoDB
> * Definiowanie kolekcji i schematu mongodb
> * Wykonywanie operacji MONGODB CRUD z internetowego interfejsu API
> * Dostosowywanie serializacji JSON

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* [.NET Core SDK 2.2](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) z **obciążeniem ASP.NET i tworzenia stron internetowych**
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [.NET Core SDK 2.2](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio Code](https://code.visualstudio.com/download)
* [Rozszerzenie C# for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [MongoDB](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* [.NET Core SDK 2.2](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio dla komputerów Mac w wersji 7.7 lub nowszej](https://visualstudio.microsoft.com/downloads/)
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a>Konfigurowanie bazy danych MongoDB

Jeśli używasz systemu Windows, MongoDB jest zainstalowany w *C:\\Program Files\\MongoDB* domyślnie. Dodaj *C:\\\\Pliki programów MongoDB\\Server\\\<version_number \\>* `Path` pojemnik do zmiennej środowiskowej. Ta zmiana umożliwia dostęp MongoDB z dowolnego miejsca na komputerze deweloperskim.

Użyj powłoki mongo w poniższych krokach, aby utworzyć bazę danych, utworzyć kolekcje i przechowywać dokumenty. Aby uzyskać więcej informacji na temat poleceń mongo Shell, zobacz [Praca z powłoką mongo](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).

1. Wybierz katalog na komputerze deweloperskim do przechowywania danych. Na przykład *C:\\BooksData* w systemie Windows. Utwórz katalog, jeśli nie istnieje. Mongo Shell nie tworzy nowych katalogów.
1. Otwórz powłokę polecenia. Uruchom następujące polecenie, aby połączyć się z mongodb na domyślnym porcie 27017. Pamiętaj, `<data_directory_path>` aby zastąpić katalog wybrany w poprzednim kroku.

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. Otwórz inne wystąpienie powłoki polecenia. Połącz się z domyślną bazą danych testów, uruchamiając następujące polecenie:

   ```console
   mongo
   ```

1. Uruchom następujące polecenie w powłoce polecenia:

   ```console
   use BookstoreDb
   ```

   Jeśli jeszcze nie istnieje, tworzona jest baza danych o nazwie *BookstoreDb.* Jeśli baza danych istnieje, jej połączenie jest otwierane dla transakcji.

1. Utwórz `Books` kolekcję za pomocą następującego polecenia:

   ```console
   db.createCollection('Books')
   ```

   Wyświetlany jest następujący wynik:

   ```console
   { "ok" : 1 }
   ```

1. Zdefiniuj `Books` schemat kolekcji i wstaw dwa dokumenty za pomocą następującego polecenia:

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   Wyświetlany jest następujący wynik:

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
   > Identyfikator jest wyświetlany w tym artykule nie będzie zgodne identyfikatory po uruchomieniu tego przykładu.

1. Wyświetlanie dokumentów w bazie danych za pomocą następującego polecenia:

   ```console
   db.Books.find({}).pretty()
   ```

   Wyświetlany jest następujący wynik:

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

   Schemat dodaje autoryzowaną `_id` właściwość `ObjectId` typu dla każdego dokumentu.

Baza danych jest gotowa. Można rozpocząć tworzenie ASP.NET Core interfejsu API sieci web.

## <a name="create-the-aspnet-core-web-api-project"></a>Tworzenie projektu internetowego interfejsu API ASP.NET Core

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

1. Przejdź do **pliku** > **nowego** > **projektu**.
1. Wybierz typ projektu **ASP.NET Core Web Application** i wybierz przycisk **Dalej**.
1. Nazwij projekt *BooksApi*i wybierz pozycję **Utwórz**.
1. Wybierz platformę docelową **.NET Core** i **ASP.NET Core 2.2**. Wybierz szablon projektu **interfejsu API** i wybierz pozycję **Utwórz**.
1. Odwiedź [Galerię NuGet: MongoDB.Driver,](https://www.nuget.org/packages/MongoDB.Driver/) aby określić najnowszą stabilną wersję sterownika .NET dla mongodb. W oknie **Konsoli Menedżera pakietów** przejdź do katalogu głównego projektu. Uruchom następujące polecenie, aby zainstalować sterownik .NET dla usługi MongoDB:

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Uruchom następujące polecenia w powłoce polecenia:

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   Nowy projekt interfejsu API sieci web ASP.NET Core jest generowany i otwierany w programie Visual Studio Code.

1. Po pasku stanu OmniSharp płomień ikona zmieni kolor na zielony, okno dialogowe pyta **wymagane zasoby do tworzenia i debugowania brakuje "BooksApi". Dodać je?**. Wybierz **pozycję Tak**.
1. Odwiedź [Galerię NuGet: MongoDB.Driver,](https://www.nuget.org/packages/MongoDB.Driver/) aby określić najnowszą stabilną wersję sterownika .NET dla mongodb. Otwórz **zintegrowany terminal** i przejdź do katalogu głównego projektu. Uruchom następujące polecenie, aby zainstalować sterownik .NET dla usługi MongoDB:

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

1. Przejdź do **pliku** > **nowego rozwiązania** > **.NET Core** > **App**.
1. Wybierz szablon projektu **ASP.NET Core Web API** C# i wybierz pozycję **Dalej**.
1. Z listy rozwijanej **"Struktura docelowa"** wybierz pozycję **.NET Core 2.2,** a następnie wybierz pozycję **Dalej**.
1. Wprowadź *pozycję BooksApi* dla **nazwy projektu**i wybierz pozycję **Utwórz**.
1. W konsoli **Rozwiązania** kliknij prawym przyciskiem myszy węzeł **Zależności** projektu i wybierz polecenie **Dodaj pakiety**.
1. Wprowadź *mongoDB.driver* w polu wyszukiwania, wybierz pakiet *MongoDB.Driver* i wybierz **dodaj pakiet**.
1. Wybierz przycisk **Zaakceptuj** w oknie dialogowym **Akceptacja licencji.**

---

## <a name="add-an-entity-model"></a>Dodawanie modelu encji

1. Dodaj katalog *Models* do katalogu głównego projektu.
1. Dodaj `Book` klasę do katalogu *Models* z następującym kodem:

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

   W poprzedniej klasie `Id` właściwość:

   * Jest wymagane do mapowania obiektu środowiska wykonawczego języka wspólnego (CLR) do kolekcji MongoDB.
   * Jest oś [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) tym adnotacjami, aby wyznaczyć tę właściwość jako klucz podstawowy dokumentu.
   * Jest opisywany [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) z aby umożliwić przekazywanie parametru jako typu `string` zamiast [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) struktury. Mongo obsługuje konwersję `string` `ObjectId`z do .

   Właściwość `BookName` jest opisywana [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) z atrybutem. Wartość atrybutu `Name` reprezentuje nazwę właściwości w kolekcji MongoDB.

## <a name="add-a-configuration-model"></a>Dodawanie modelu konfiguracji

1. Dodaj następujące wartości konfiguracji bazy danych do *pliku appsettings.json:*

   [!code-json[](first-mongo-app/samples/2.x/SampleApp/appsettings.json?highlight=2-6)]

1. Dodaj plik *BookstoreDatabaseSettings.cs* do katalogu *Models* z następującym kodem:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   Poprzednia `BookstoreDatabaseSettings` klasa jest używana do przechowywania wartości `BookstoreDatabaseSettings` właściwości pliku *appsettings.json.* Nazwy właściwości JSON i C# są nazwane identycznie, aby ułatwić proces mapowania.

1. Dodaj następujący wyróżniony `Startup.ConfigureServices`kod do:

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   Powyższy kod ma następujące działanie:

   * Wystąpienie konfiguracji, z którym wiąże `BookstoreDatabaseSettings` się sekcja pliku *appsettings.json,* jest zarejestrowane w kontenerze iniekcji zależności (DI). Na przykład `BookstoreDatabaseSettings` właściwość `ConnectionString` obiektu jest wypełniona właściwością `BookstoreDatabaseSettings:ConnectionString` w *pliku appsettings.json*.
   * Interfejs `IBookstoreDatabaseSettings` jest zarejestrowany w DI z [okresu istnienia usługi](xref:fundamentals/dependency-injection#service-lifetimes)singleton . Po wstrzyknięciu wystąpienie interfejsu jest `BookstoreDatabaseSettings` rozpoznawane dla obiektu.

1. Dodaj następujący kod do górnej części *Startup.cs,* aby rozwiązać `BookstoreDatabaseSettings` i `IBookstoreDatabaseSettings` odwołania:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a>Dodawanie usługi operacji CRUD

1. Dodaj katalog *usług* do katalogu głównego projektu.
1. Dodaj `BookService` klasę do katalogu *usług* z następującym kodem:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   W poprzednim kodzie `IBookstoreDatabaseSettings` wystąpienie jest pobierane z DI za pośrednictwem iniekcji konstruktora. Ta technika zapewnia dostęp do wartości konfiguracji *appsettings.json,* które zostały dodane w sekcji [Dodaj model konfiguracji.](#add-a-configuration-model)

1. Dodaj następujący wyróżniony `Startup.ConfigureServices`kod do:

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   W poprzednim kodzie `BookService` klasa jest zarejestrowana w DI do obsługi iniekcji konstruktora w klasach zużywających. Okres istnienia usługi singleton `BookService` jest najbardziej odpowiedni, `MongoClient`ponieważ przyjmuje bezpośrednią zależność od . Zgodnie z [oficjalnymi wytycznymi mongo klienta ponownego wykorzystania,](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use) `MongoClient` powinny być zarejestrowane w DI z okresu istnienia usługi singleton.

1. Dodaj następujący kod do górnej części *Startup.cs,* aby rozwiązać `BookService` odwołanie:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

Klasa `BookService` używa następujących `MongoDB.Driver` elementów członkowskich do wykonywania operacji CRUD w bazie danych:

* [MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; odczytuje wystąpienie serwera do wykonywania operacji bazy danych. Konstruktor tej klasy jest pod warunkiem parametry połączenia MongoDB:

  [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* [IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Reprezentuje mongo bazy danych do wykonywania operacji. W tym samouczku użyto ogólnej metody [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) w interfejsie, aby uzyskać dostęp do danych w określonej kolekcji. Wykonaj operacje CRUD względem kolekcji po wywołaniu tej metody. W `GetCollection<TDocument>(collection)` wywołaniu metody:

  * `collection`reprezentuje nazwę kolekcji.
  * `TDocument`reprezentuje typ obiektu CLR przechowywane w kolekcji.

`GetCollection<TDocument>(collection)`zwraca [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) obiektu reprezentującego kolekcję. W tym samouczku są wywoływane w kolekcji następujące metody:

* [DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Usuwa pojedynczy dokument spełniający podane kryteria wyszukiwania.
* [Znajdź\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Zwraca wszystkie dokumenty w kolekcji zgodne z podanymi kryteriami wyszukiwania.
* [InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Wstawia podany obiekt jako nowy dokument w kolekcji.
* [ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Zastępuje pojedynczy dokument pasujący do podanych kryteriów wyszukiwania z podanym obiektem.

## <a name="add-a-controller"></a>Dodawanie kontrolera

Dodaj `BooksController` klasę do katalogu *Controllers* z następującym kodem:

[!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Controllers/BooksController.cs)]

Poprzedni kontroler interfejsu API sieci web:

* Używa `BookService` klasy do wykonywania operacji CRUD.
* Zawiera metody akcji do obsługi żądań HTTP GET, POST, PUT i DELETE.
* Wywołania <xref:System.Web.Http.ApiController.CreatedAtRoute*> `Create` w metodzie akcji, aby zwrócić odpowiedź [HTTP 201.](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) Kod stanu 201 jest standardową odpowiedzią dla metody HTTP POST, która tworzy nowy zasób na serwerze. `CreatedAtRoute`również dodaje `Location` nagłówek do odpowiedzi. Nagłówek `Location` określa identyfikator URI nowo utworzonej książki.

## <a name="test-the-web-api"></a>Testowanie internetowego interfejsu API

1. Skompiluj i uruchom aplikację.

1. Przejdź `http://localhost:<port>/api/books` do, aby przetestować `Get` metodę akcji bez parametrów kontrolera. Wyświetlana jest następująca odpowiedź JSON:

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

1. Przejdź `http://localhost:<port>/api/books/{id here}` do, aby przetestować metodę `Get` akcji przeciążone kontrolera. Wyświetlana jest następująca odpowiedź JSON:

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

Istnieją dwa szczegóły do zmiany dotyczące odpowiedzi JSON zwróconych w sekcji [Testowanie interfejsu API sieci web:](#test-the-web-api)

* Domyślna wielkość liter na wielbłądzie nazw właściwości powinna zostać zmieniona tak, aby odpowiadała wielkości liter pascalowej nazw właściwości obiektu CLR.
* Właściwość `bookName` powinna zostać `Name`zwrócona jako .

Aby spełnić powyższe wymagania, należy wprowadzić następujące zmiany:

1. W `Startup.ConfigureServices`, łańcuch następujący wyróżniony `AddMvc` kod na wywołanie metody:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   Wraz z poprzednią zmianą nazwy właściwości w serializowanej odpowiedzi JSON interfejsu API sieci web są zgodne z odpowiadającymi im nazwami właściwości w typie obiektu CLR. Na przykład `Book` `Author` właściwość klasy serializuje `Author`się jako .

1. W *models/book.cs*, adnotacje `BookName` właściwości [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) z następującym atrybutem:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   Wartość `[JsonProperty]` atrybutu `Name` reprezentuje nazwę właściwości w serializowanej odpowiedzi JSON interfejsu API sieci web.

1. Dodaj następujący kod do góry *Models/Book.cs,* aby rozwiązać odwołanie do atrybutu: `[JsonProperty]`

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. Powtórz kroki zdefiniowane w sekcji [Testowanie internetowego interfejsu API.](#test-the-web-api) Zwróć uwagę na różnicę w nazwach właściwości JSON.

::: moniker-end

## <a name="add-authentication-support-to-a-web-api"></a>Dodawanie obsługi uwierzytelniania do internetowego interfejsu API

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat tworzenia ASP.NET podstawowych interfejsów API sieci web, zobacz następujące zasoby:

* [Wersja tego artykułu w YouTube](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
