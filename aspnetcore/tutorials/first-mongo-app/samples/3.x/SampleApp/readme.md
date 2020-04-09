---
page_type: sample
description: W tym samouczku pokazano, jak utworzyć ASP.NET core internetowego interfejsu API przy użyciu bazy danych MongoDB NoSQL.
languages:
- csharp
products:
- dotnet-core
- aspnet-core
- vs
urlFragment: aspnetcore-webapi-mongodb
ms.openlocfilehash: 09d73e25667822b8748a00cc76ad6d4f0e5fe290
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511408"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a>Tworzenie internetowego interfejsu API z ASP.NET Core i MongoDB

W tym samouczku utworzy się internetowy interfejs API, który wykonuje operacje tworzenia, odczytu, aktualizacji i usuwania (CRUD) w bazie danych [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

* Konfigurowanie bazy danych MongoDB
* Tworzenie bazy danych MongoDB
* Definiowanie kolekcji i schematu mongodb
* Wykonywanie operacji MONGODB CRUD z internetowego interfejsu API
* Dostosowywanie serializacji JSON

## <a name="prerequisites"></a>Wymagania wstępne

* [Zestaw .NET Core SDK 3.0 lub nowszy](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio 2019 — wersja zapoznawcza](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=community&ch=pre&rel=16&utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019preview) z obciążeniem **ASP.NET i tworzenia stron internetowych**
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

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

1. Przejdź do **pliku** > **nowego** > **projektu**.
1. Wybierz typ projektu **ASP.NET Core Web Application** i wybierz przycisk **Dalej**.
1. Nazwij projekt *BooksApi*i wybierz pozycję **Utwórz**.
1. Wybierz platformę docelową **.NET Core** i **ASP.NET Core 3.0**. Wybierz szablon projektu **interfejsu API** i wybierz pozycję **Utwórz**.
1. Odwiedź [Galerię NuGet: MongoDB.Driver,](https://www.nuget.org/packages/MongoDB.Driver/) aby określić najnowszą stabilną wersję sterownika .NET dla mongodb. W oknie **Konsoli Menedżera pakietów** przejdź do katalogu głównego projektu. Uruchom następujące polecenie, aby zainstalować sterownik .NET dla usługi MongoDB:

    ```powershell
    Install-Package MongoDB.Driver -Version {VERSION}
    ```

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

    ```javascript
    {
      "BookstoreDatabaseSettings": {
        "BooksCollectionName": "Books",
        "ConnectionString": "mongodb://localhost:27017",
        "DatabaseName": "BookstoreDb"
      },

    ```

1. Dodaj plik *BookstoreDatabaseSettings.cs* do katalogu *Models* z następującym kodem:

    ```csharp
    namespace BooksApi.Models
    {
        public class BookstoreDatabaseSettings : IBookstoreDatabaseSettings
        {
            public string BooksCollectionName { get; set; }
            public string ConnectionString { get; set; }
            public string DatabaseName { get; set; }
        }

        public interface IBookstoreDatabaseSettings
        {
            string BooksCollectionName { get; set; }
            string ConnectionString { get; set; }
            string DatabaseName { get; set; }
        }
    }
    ```

    Poprzednia `BookstoreDatabaseSettings` klasa jest używana do przechowywania wartości `BookstoreDatabaseSettings` właściwości pliku *appsettings.json.* Nazwy właściwości JSON i C# są nazwane identycznie, aby ułatwić proces mapowania.

1. Dodaj następujący wyróżniony `Startup.ConfigureServices`kod do:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<BookstoreDatabaseSettings>(
            Configuration.GetSection(nameof(BookstoreDatabaseSettings)));

        services.AddSingleton<IBookstoreDatabaseSettings>(sp =>
            sp.GetRequiredService<IOptions<BookstoreDatabaseSettings>>().Value);

        services.AddControllers();
    }
    ```

    Powyższy kod ma następujące działanie:

    * Wystąpienie konfiguracji, z którym wiąże `BookstoreDatabaseSettings` się sekcja pliku *appsettings.json,* jest zarejestrowane w kontenerze iniekcji zależności (DI). Na przykład `BookstoreDatabaseSettings` właściwość `ConnectionString` obiektu jest wypełniona właściwością `BookstoreDatabaseSettings:ConnectionString` w *pliku appsettings.json*.
    * Interfejs `IBookstoreDatabaseSettings` jest zarejestrowany w DI z [okresu istnienia usługi](xref:fundamentals/dependency-injection#service-lifetimes)singleton . Po wstrzyknięciu wystąpienie interfejsu jest `BookstoreDatabaseSettings` rozpoznawane dla obiektu.

1. Dodaj następujący kod do górnej części *Startup.cs,* aby rozwiązać `BookstoreDatabaseSettings` i `IBookstoreDatabaseSettings` odwołania:

    ```csharp
    using BooksApi.Models;
    ```

## <a name="add-a-crud-operations-service"></a>Dodawanie usługi operacji CRUD

1. Dodaj katalog *usług* do katalogu głównego projektu.
1. Dodaj `BookService` klasę do katalogu *usług* z następującym kodem:

    ```csharp
    using BooksApi.Models;
    using MongoDB.Driver;
    using System.Collections.Generic;
    using System.Linq;

    namespace BooksApi.Services
    {
        public class BookService
        {
            private readonly IMongoCollection<Book> _books;

            public BookService(IBookstoreDatabaseSettings settings)
            {
                var client = new MongoClient(settings.ConnectionString);
                var database = client.GetDatabase(settings.DatabaseName);

                _books = database.GetCollection<Book>(settings.BooksCollectionName);
            }

            public List<Book> Get() =>
                _books.Find(book => true).ToList();

            public Book Get(string id) =>
                _books.Find<Book>(book => book.Id == id).FirstOrDefault();

            public Book Create(Book book)
            {
                _books.InsertOne(book);
                return book;
            }

            public void Update(string id, Book bookIn) =>
                _books.ReplaceOne(book => book.Id == id, bookIn);

            public void Remove(Book bookIn) =>
                _books.DeleteOne(book => book.Id == bookIn.Id);

            public void Remove(string id) =>
                _books.DeleteOne(book => book.Id == id);
        }
    }
    ```

    W poprzednim kodzie `IBookstoreDatabaseSettings` wystąpienie jest pobierane z DI za pośrednictwem iniekcji konstruktora. Ta technika zapewnia dostęp do wartości konfiguracji *appsettings.json,* które zostały dodane w sekcji [Dodaj model konfiguracji.](#add-a-configuration-model)

1. Dodaj następujący wyróżniony `Startup.ConfigureServices`kod do:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<BookstoreDatabaseSettings>(
            Configuration.GetSection(nameof(BookstoreDatabaseSettings)));

        services.AddSingleton<IBookstoreDatabaseSettings>(sp =>
            sp.GetRequiredService<IOptions<BookstoreDatabaseSettings>>().Value);

        services.AddSingleton<BookService>();

        services.AddControllers();
    }
    ```

    W poprzednim kodzie `BookService` klasa jest zarejestrowana w DI do obsługi iniekcji konstruktora w klasach zużywających. Okres istnienia usługi singleton `BookService` jest najbardziej odpowiedni, `MongoClient`ponieważ przyjmuje bezpośrednią zależność od . Zgodnie z [oficjalnymi wytycznymi mongo klienta ponownego wykorzystania,](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use) `MongoClient` powinny być zarejestrowane w DI z okresu istnienia usługi singleton.

1. Dodaj następujący kod do górnej części *Startup.cs,* aby rozwiązać `BookService` odwołanie:


    ```csharp
    using BooksApi.Services;
    ```

Klasa `BookService` używa następujących `MongoDB.Driver` elementów członkowskich do wykonywania operacji CRUD w bazie danych:

* [MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; odczytuje wystąpienie serwera do wykonywania operacji bazy danych. Konstruktor tej klasy jest pod warunkiem parametry połączenia MongoDB:

    ```csharp
    public BookService(IBookstoreDatabaseSettings settings)
    {
        var client = new MongoClient(settings.ConnectionString);
        var database = client.GetDatabase(settings.DatabaseName);

        _books = database.GetCollection<Book>(settings.BooksCollectionName);
    }
    ```

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

```csharp
using BooksApi.Models;
using BooksApi.Services;
using Microsoft.AspNetCore.Mvc;
using System.Collections.Generic;

namespace BooksApi.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class BooksController : ControllerBase
    {
        private readonly BookService _bookService;

        public BooksController(BookService bookService)
        {
            _bookService = bookService;
        }

        [HttpGet]
        public ActionResult<List<Book>> Get() =>
            _bookService.Get();

        [HttpGet("{id:length(24)}", Name = "GetBook")]
        public ActionResult<Book> Get(string id)
        {
            var book = _bookService.Get(id);

            if (book == null)
            {
                return NotFound();
            }

            return book;
        }

        [HttpPost]
        public ActionResult<Book> Create(Book book)
        {
            _bookService.Create(book);

            return CreatedAtRoute("GetBook", new { id = book.Id.ToString() }, book);
        }

        [HttpPut("{id:length(24)}")]
        public IActionResult Update(string id, Book bookIn)
        {
            var book = _bookService.Get(id);

            if (book == null)
            {
                return NotFound();
            }

            _bookService.Update(id, bookIn);

            return NoContent();
        }

        [HttpDelete("{id:length(24)}")]
        public IActionResult Delete(string id)
        {
            var book = _bookService.Get(id);

            if (book == null)
            {
                return NotFound();
            }

            _bookService.Remove(book.Id);

            return NoContent();
        }
    }
}
```

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

1. W `Startup.ConfigureServices`, łańcuch następujący wyróżniony `AddMvc` kod na wywołanie metody:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<BookstoreDatabaseSettings>(
            Configuration.GetSection(nameof(BookstoreDatabaseSettings)));

        services.AddSingleton<IBookstoreDatabaseSettings>(sp =>
            sp.GetRequiredService<IOptions<BookstoreDatabaseSettings>>().Value);

        services.AddSingleton<BookService>();

        services.AddControllers()
            .AddNewtonsoftJson(options => options.UseMemberCasing());
    }
    ```

    Wraz z poprzednią zmianą nazwy właściwości w serializowanej odpowiedzi JSON interfejsu API sieci web są zgodne z odpowiadającymi im nazwami właściwości w typie obiektu CLR. Na przykład `Book` `Author` właściwość klasy serializuje `Author`się jako .

1. W *models/book.cs*, adnotacje `BookName` właściwości [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) z następującym atrybutem:

    ```csharp
    [BsonElement("Name")]
    [JsonProperty("Name")]
    public string BookName { get; set; }
    ```

    Wartość `[JsonProperty]` atrybutu `Name` reprezentuje nazwę właściwości w serializowanej odpowiedzi JSON interfejsu API sieci web.

1. Dodaj następujący kod do góry *Models/Book.cs,* aby rozwiązać odwołanie do atrybutu: `[JsonProperty]`

    ```csharp
    using Newtonsoft.Json;
    ```

1. Powtórz kroki zdefiniowane w sekcji [Testowanie internetowego interfejsu API.](#test-the-web-api) Zwróć uwagę na różnicę w nazwach właściwości JSON.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat tworzenia ASP.NET podstawowych interfejsów API sieci web, zobacz następujące zasoby:

* [Wersja tego artykułu w YouTube](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* [Tworzenie interfejsów API sieci Web za pomocą ASP.NET Core](https://docs.microsoft.com/aspnet/core/web-api/index?view=aspnetcore-3.0)
