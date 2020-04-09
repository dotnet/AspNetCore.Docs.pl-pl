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
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="4302f-102">Tworzenie internetowego interfejsu API z ASP.NET Core i MongoDB</span><span class="sxs-lookup"><span data-stu-id="4302f-102">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="4302f-103">W tym samouczku utworzy się internetowy interfejs API, który wykonuje operacje tworzenia, odczytu, aktualizacji i usuwania (CRUD) w bazie danych [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL.</span><span class="sxs-lookup"><span data-stu-id="4302f-103">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="4302f-104">Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="4302f-104">In this tutorial, you learn how to:</span></span>

* <span data-ttu-id="4302f-105">Konfigurowanie bazy danych MongoDB</span><span class="sxs-lookup"><span data-stu-id="4302f-105">Configure MongoDB</span></span>
* <span data-ttu-id="4302f-106">Tworzenie bazy danych MongoDB</span><span class="sxs-lookup"><span data-stu-id="4302f-106">Create a MongoDB database</span></span>
* <span data-ttu-id="4302f-107">Definiowanie kolekcji i schematu mongodb</span><span class="sxs-lookup"><span data-stu-id="4302f-107">Define a MongoDB collection and schema</span></span>
* <span data-ttu-id="4302f-108">Wykonywanie operacji MONGODB CRUD z internetowego interfejsu API</span><span class="sxs-lookup"><span data-stu-id="4302f-108">Perform MongoDB CRUD operations from a web API</span></span>
* <span data-ttu-id="4302f-109">Dostosowywanie serializacji JSON</span><span class="sxs-lookup"><span data-stu-id="4302f-109">Customize JSON serialization</span></span>

## <a name="prerequisites"></a><span data-ttu-id="4302f-110">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="4302f-110">Prerequisites</span></span>

* [<span data-ttu-id="4302f-111">Zestaw .NET Core SDK 3.0 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="4302f-111">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="4302f-112">[Visual Studio 2019 — wersja zapoznawcza](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=community&ch=pre&rel=16&utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019preview) z obciążeniem **ASP.NET i tworzenia stron internetowych**</span><span class="sxs-lookup"><span data-stu-id="4302f-112">[Visual Studio 2019 Preview](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=community&ch=pre&rel=16&utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019preview) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="4302f-113">MongoDB</span><span class="sxs-lookup"><span data-stu-id="4302f-113">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

## <a name="configure-mongodb"></a><span data-ttu-id="4302f-114">Konfigurowanie bazy danych MongoDB</span><span class="sxs-lookup"><span data-stu-id="4302f-114">Configure MongoDB</span></span>

<span data-ttu-id="4302f-115">Jeśli używasz systemu Windows, MongoDB jest zainstalowany w *C:\\Program Files\\MongoDB* domyślnie.</span><span class="sxs-lookup"><span data-stu-id="4302f-115">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="4302f-116">Dodaj *C:\\\\Pliki programów MongoDB\\Server\\\<version_number \\>* `Path` pojemnik do zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="4302f-116">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="4302f-117">Ta zmiana umożliwia dostęp MongoDB z dowolnego miejsca na komputerze deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="4302f-117">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="4302f-118">Użyj powłoki mongo w poniższych krokach, aby utworzyć bazę danych, utworzyć kolekcje i przechowywać dokumenty.</span><span class="sxs-lookup"><span data-stu-id="4302f-118">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="4302f-119">Aby uzyskać więcej informacji na temat poleceń mongo Shell, zobacz [Praca z powłoką mongo](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="4302f-119">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="4302f-120">Wybierz katalog na komputerze deweloperskim do przechowywania danych.</span><span class="sxs-lookup"><span data-stu-id="4302f-120">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="4302f-121">Na przykład *C:\\BooksData* w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="4302f-121">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="4302f-122">Utwórz katalog, jeśli nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="4302f-122">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="4302f-123">Mongo Shell nie tworzy nowych katalogów.</span><span class="sxs-lookup"><span data-stu-id="4302f-123">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="4302f-124">Otwórz powłokę polecenia.</span><span class="sxs-lookup"><span data-stu-id="4302f-124">Open a command shell.</span></span> <span data-ttu-id="4302f-125">Uruchom następujące polecenie, aby połączyć się z mongodb na domyślnym porcie 27017.</span><span class="sxs-lookup"><span data-stu-id="4302f-125">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="4302f-126">Pamiętaj, `<data_directory_path>` aby zastąpić katalog wybrany w poprzednim kroku.</span><span class="sxs-lookup"><span data-stu-id="4302f-126">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

    ```console
    mongod --dbpath <data_directory_path>
    ```

1. <span data-ttu-id="4302f-127">Otwórz inne wystąpienie powłoki polecenia.</span><span class="sxs-lookup"><span data-stu-id="4302f-127">Open another command shell instance.</span></span> <span data-ttu-id="4302f-128">Połącz się z domyślną bazą danych testów, uruchamiając następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="4302f-128">Connect to the default test database by running the following command:</span></span>

    ```console
    mongo
    ```

1. <span data-ttu-id="4302f-129">Uruchom następujące polecenie w powłoce polecenia:</span><span class="sxs-lookup"><span data-stu-id="4302f-129">Run the following in a command shell:</span></span>

    ```console
    use BookstoreDb
    ```

    <span data-ttu-id="4302f-130">Jeśli jeszcze nie istnieje, tworzona jest baza danych o nazwie *BookstoreDb.*</span><span class="sxs-lookup"><span data-stu-id="4302f-130">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="4302f-131">Jeśli baza danych istnieje, jej połączenie jest otwierane dla transakcji.</span><span class="sxs-lookup"><span data-stu-id="4302f-131">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="4302f-132">Utwórz `Books` kolekcję za pomocą następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="4302f-132">Create a `Books` collection using following command:</span></span>

    ```console
    db.createCollection('Books')
    ```

    <span data-ttu-id="4302f-133">Wyświetlany jest następujący wynik:</span><span class="sxs-lookup"><span data-stu-id="4302f-133">The following result is displayed:</span></span>

    ```console
    { "ok" : 1 }
    ```

1. <span data-ttu-id="4302f-134">Zdefiniuj `Books` schemat kolekcji i wstaw dwa dokumenty za pomocą następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="4302f-134">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

    ```console
    db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
    ```

    <span data-ttu-id="4302f-135">Wyświetlany jest następujący wynik:</span><span class="sxs-lookup"><span data-stu-id="4302f-135">The following result is displayed:</span></span>

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
  > <span data-ttu-id="4302f-136">Identyfikator jest wyświetlany w tym artykule nie będzie zgodne identyfikatory po uruchomieniu tego przykładu.</span><span class="sxs-lookup"><span data-stu-id="4302f-136">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="4302f-137">Wyświetlanie dokumentów w bazie danych za pomocą następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="4302f-137">View the documents in the database using the following command:</span></span>

    ```console
    db.Books.find({}).pretty()
    ```

    <span data-ttu-id="4302f-138">Wyświetlany jest następujący wynik:</span><span class="sxs-lookup"><span data-stu-id="4302f-138">The following result is displayed:</span></span>

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

    <span data-ttu-id="4302f-139">Schemat dodaje autoryzowaną `_id` właściwość `ObjectId` typu dla każdego dokumentu.</span><span class="sxs-lookup"><span data-stu-id="4302f-139">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="4302f-140">Baza danych jest gotowa.</span><span class="sxs-lookup"><span data-stu-id="4302f-140">The database is ready.</span></span> <span data-ttu-id="4302f-141">Można rozpocząć tworzenie ASP.NET Core interfejsu API sieci web.</span><span class="sxs-lookup"><span data-stu-id="4302f-141">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="4302f-142">Tworzenie projektu internetowego interfejsu API ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4302f-142">Create the ASP.NET Core web API project</span></span>

1. <span data-ttu-id="4302f-143">Przejdź do **pliku** > **nowego** > **projektu**.</span><span class="sxs-lookup"><span data-stu-id="4302f-143">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="4302f-144">Wybierz typ projektu **ASP.NET Core Web Application** i wybierz przycisk **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="4302f-144">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="4302f-145">Nazwij projekt *BooksApi*i wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="4302f-145">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="4302f-146">Wybierz platformę docelową **.NET Core** i **ASP.NET Core 3.0**.</span><span class="sxs-lookup"><span data-stu-id="4302f-146">Select the **.NET Core** target framework and **ASP.NET Core 3.0**.</span></span> <span data-ttu-id="4302f-147">Wybierz szablon projektu **interfejsu API** i wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="4302f-147">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="4302f-148">Odwiedź [Galerię NuGet: MongoDB.Driver,](https://www.nuget.org/packages/MongoDB.Driver/) aby określić najnowszą stabilną wersję sterownika .NET dla mongodb.</span><span class="sxs-lookup"><span data-stu-id="4302f-148">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="4302f-149">W oknie **Konsoli Menedżera pakietów** przejdź do katalogu głównego projektu.</span><span class="sxs-lookup"><span data-stu-id="4302f-149">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="4302f-150">Uruchom następujące polecenie, aby zainstalować sterownik .NET dla usługi MongoDB:</span><span class="sxs-lookup"><span data-stu-id="4302f-150">Run the following command to install the .NET driver for MongoDB:</span></span>

    ```powershell
    Install-Package MongoDB.Driver -Version {VERSION}
    ```

## <a name="add-an-entity-model"></a><span data-ttu-id="4302f-151">Dodawanie modelu encji</span><span class="sxs-lookup"><span data-stu-id="4302f-151">Add an entity model</span></span>

1. <span data-ttu-id="4302f-152">Dodaj katalog *Models* do katalogu głównego projektu.</span><span class="sxs-lookup"><span data-stu-id="4302f-152">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="4302f-153">Dodaj `Book` klasę do katalogu *Models* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="4302f-153">Add a `Book` class to the *Models* directory with the following code:</span></span>

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

    <span data-ttu-id="4302f-154">W poprzedniej klasie `Id` właściwość:</span><span class="sxs-lookup"><span data-stu-id="4302f-154">In the preceding class, the `Id` property:</span></span>

    * <span data-ttu-id="4302f-155">Jest wymagane do mapowania obiektu środowiska wykonawczego języka wspólnego (CLR) do kolekcji MongoDB.</span><span class="sxs-lookup"><span data-stu-id="4302f-155">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
    * <span data-ttu-id="4302f-156">Jest oś [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) tym adnotacjami, aby wyznaczyć tę właściwość jako klucz podstawowy dokumentu.</span><span class="sxs-lookup"><span data-stu-id="4302f-156">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
    * <span data-ttu-id="4302f-157">Jest opisywany [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) z aby umożliwić przekazywanie parametru jako typu `string` zamiast [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) struktury.</span><span class="sxs-lookup"><span data-stu-id="4302f-157">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="4302f-158">Mongo obsługuje konwersję `string` `ObjectId`z do .</span><span class="sxs-lookup"><span data-stu-id="4302f-158">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

    <span data-ttu-id="4302f-159">Właściwość `BookName` jest opisywana [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) z atrybutem.</span><span class="sxs-lookup"><span data-stu-id="4302f-159">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="4302f-160">Wartość atrybutu `Name` reprezentuje nazwę właściwości w kolekcji MongoDB.</span><span class="sxs-lookup"><span data-stu-id="4302f-160">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="4302f-161">Dodawanie modelu konfiguracji</span><span class="sxs-lookup"><span data-stu-id="4302f-161">Add a configuration model</span></span>

1. <span data-ttu-id="4302f-162">Dodaj następujące wartości konfiguracji bazy danych do *pliku appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="4302f-162">Add the following database configuration values to *appsettings.json*:</span></span>

    ```javascript
    {
      "BookstoreDatabaseSettings": {
        "BooksCollectionName": "Books",
        "ConnectionString": "mongodb://localhost:27017",
        "DatabaseName": "BookstoreDb"
      },

    ```

1. <span data-ttu-id="4302f-163">Dodaj plik *BookstoreDatabaseSettings.cs* do katalogu *Models* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="4302f-163">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

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

    <span data-ttu-id="4302f-164">Poprzednia `BookstoreDatabaseSettings` klasa jest używana do przechowywania wartości `BookstoreDatabaseSettings` właściwości pliku *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="4302f-164">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="4302f-165">Nazwy właściwości JSON i C# są nazwane identycznie, aby ułatwić proces mapowania.</span><span class="sxs-lookup"><span data-stu-id="4302f-165">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="4302f-166">Dodaj następujący wyróżniony `Startup.ConfigureServices`kod do:</span><span class="sxs-lookup"><span data-stu-id="4302f-166">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

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

    <span data-ttu-id="4302f-167">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="4302f-167">In the preceding code:</span></span>

    * <span data-ttu-id="4302f-168">Wystąpienie konfiguracji, z którym wiąże `BookstoreDatabaseSettings` się sekcja pliku *appsettings.json,* jest zarejestrowane w kontenerze iniekcji zależności (DI).</span><span class="sxs-lookup"><span data-stu-id="4302f-168">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="4302f-169">Na przykład `BookstoreDatabaseSettings` właściwość `ConnectionString` obiektu jest wypełniona właściwością `BookstoreDatabaseSettings:ConnectionString` w *pliku appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="4302f-169">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
    * <span data-ttu-id="4302f-170">Interfejs `IBookstoreDatabaseSettings` jest zarejestrowany w DI z [okresu istnienia usługi](xref:fundamentals/dependency-injection#service-lifetimes)singleton .</span><span class="sxs-lookup"><span data-stu-id="4302f-170">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="4302f-171">Po wstrzyknięciu wystąpienie interfejsu jest `BookstoreDatabaseSettings` rozpoznawane dla obiektu.</span><span class="sxs-lookup"><span data-stu-id="4302f-171">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="4302f-172">Dodaj następujący kod do górnej części *Startup.cs,* aby rozwiązać `BookstoreDatabaseSettings` i `IBookstoreDatabaseSettings` odwołania:</span><span class="sxs-lookup"><span data-stu-id="4302f-172">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

    ```csharp
    using BooksApi.Models;
    ```

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="4302f-173">Dodawanie usługi operacji CRUD</span><span class="sxs-lookup"><span data-stu-id="4302f-173">Add a CRUD operations service</span></span>

1. <span data-ttu-id="4302f-174">Dodaj katalog *usług* do katalogu głównego projektu.</span><span class="sxs-lookup"><span data-stu-id="4302f-174">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="4302f-175">Dodaj `BookService` klasę do katalogu *usług* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="4302f-175">Add a `BookService` class to the *Services* directory with the following code:</span></span>

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

    <span data-ttu-id="4302f-176">W poprzednim kodzie `IBookstoreDatabaseSettings` wystąpienie jest pobierane z DI za pośrednictwem iniekcji konstruktora.</span><span class="sxs-lookup"><span data-stu-id="4302f-176">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="4302f-177">Ta technika zapewnia dostęp do wartości konfiguracji *appsettings.json,* które zostały dodane w sekcji [Dodaj model konfiguracji.](#add-a-configuration-model)</span><span class="sxs-lookup"><span data-stu-id="4302f-177">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="4302f-178">Dodaj następujący wyróżniony `Startup.ConfigureServices`kod do:</span><span class="sxs-lookup"><span data-stu-id="4302f-178">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

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

    <span data-ttu-id="4302f-179">W poprzednim kodzie `BookService` klasa jest zarejestrowana w DI do obsługi iniekcji konstruktora w klasach zużywających.</span><span class="sxs-lookup"><span data-stu-id="4302f-179">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="4302f-180">Okres istnienia usługi singleton `BookService` jest najbardziej odpowiedni, `MongoClient`ponieważ przyjmuje bezpośrednią zależność od .</span><span class="sxs-lookup"><span data-stu-id="4302f-180">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="4302f-181">Zgodnie z [oficjalnymi wytycznymi mongo klienta ponownego wykorzystania,](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use) `MongoClient` powinny być zarejestrowane w DI z okresu istnienia usługi singleton.</span><span class="sxs-lookup"><span data-stu-id="4302f-181">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="4302f-182">Dodaj następujący kod do górnej części *Startup.cs,* aby rozwiązać `BookService` odwołanie:</span><span class="sxs-lookup"><span data-stu-id="4302f-182">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>


    ```csharp
    using BooksApi.Services;
    ```

<span data-ttu-id="4302f-183">Klasa `BookService` używa następujących `MongoDB.Driver` elementów członkowskich do wykonywania operacji CRUD w bazie danych:</span><span class="sxs-lookup"><span data-stu-id="4302f-183">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="4302f-184">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; odczytuje wystąpienie serwera do wykonywania operacji bazy danych.</span><span class="sxs-lookup"><span data-stu-id="4302f-184">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Reads the server instance for performing database operations.</span></span> <span data-ttu-id="4302f-185">Konstruktor tej klasy jest pod warunkiem parametry połączenia MongoDB:</span><span class="sxs-lookup"><span data-stu-id="4302f-185">The constructor of this class is provided the MongoDB connection string:</span></span>

    ```csharp
    public BookService(IBookstoreDatabaseSettings settings)
    {
        var client = new MongoClient(settings.ConnectionString);
        var database = client.GetDatabase(settings.DatabaseName);

        _books = database.GetCollection<Book>(settings.BooksCollectionName);
    }
    ```

* <span data-ttu-id="4302f-186">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Reprezentuje mongo bazy danych do wykonywania operacji.</span><span class="sxs-lookup"><span data-stu-id="4302f-186">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="4302f-187">W tym samouczku użyto ogólnej metody [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) w interfejsie, aby uzyskać dostęp do danych w określonej kolekcji.</span><span class="sxs-lookup"><span data-stu-id="4302f-187">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="4302f-188">Wykonaj operacje CRUD względem kolekcji po wywołaniu tej metody.</span><span class="sxs-lookup"><span data-stu-id="4302f-188">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="4302f-189">W `GetCollection<TDocument>(collection)` wywołaniu metody:</span><span class="sxs-lookup"><span data-stu-id="4302f-189">In the `GetCollection<TDocument>(collection)` method call:</span></span>
  * <span data-ttu-id="4302f-190">`collection`reprezentuje nazwę kolekcji.</span><span class="sxs-lookup"><span data-stu-id="4302f-190">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="4302f-191">`TDocument`reprezentuje typ obiektu CLR przechowywane w kolekcji.</span><span class="sxs-lookup"><span data-stu-id="4302f-191">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="4302f-192">`GetCollection<TDocument>(collection)`zwraca [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) obiektu reprezentującego kolekcję.</span><span class="sxs-lookup"><span data-stu-id="4302f-192">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="4302f-193">W tym samouczku są wywoływane w kolekcji następujące metody:</span><span class="sxs-lookup"><span data-stu-id="4302f-193">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="4302f-194">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Usuwa pojedynczy dokument spełniający podane kryteria wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="4302f-194">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="4302f-195">[Znajdź\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Zwraca wszystkie dokumenty w kolekcji zgodne z podanymi kryteriami wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="4302f-195">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="4302f-196">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Wstawia podany obiekt jako nowy dokument w kolekcji.</span><span class="sxs-lookup"><span data-stu-id="4302f-196">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="4302f-197">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Zastępuje pojedynczy dokument pasujący do podanych kryteriów wyszukiwania z podanym obiektem.</span><span class="sxs-lookup"><span data-stu-id="4302f-197">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="4302f-198">Dodawanie kontrolera</span><span class="sxs-lookup"><span data-stu-id="4302f-198">Add a controller</span></span>

<span data-ttu-id="4302f-199">Dodaj `BooksController` klasę do katalogu *Controllers* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="4302f-199">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

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

<span data-ttu-id="4302f-200">Poprzedni kontroler interfejsu API sieci web:</span><span class="sxs-lookup"><span data-stu-id="4302f-200">The preceding web API controller:</span></span>

* <span data-ttu-id="4302f-201">Używa `BookService` klasy do wykonywania operacji CRUD.</span><span class="sxs-lookup"><span data-stu-id="4302f-201">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="4302f-202">Zawiera metody akcji do obsługi żądań HTTP GET, POST, PUT i DELETE.</span><span class="sxs-lookup"><span data-stu-id="4302f-202">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="4302f-203">Wywołania <xref:System.Web.Http.ApiController.CreatedAtRoute*> `Create` w metodzie akcji, aby zwrócić odpowiedź [HTTP 201.](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)</span><span class="sxs-lookup"><span data-stu-id="4302f-203">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="4302f-204">Kod stanu 201 jest standardową odpowiedzią dla metody HTTP POST, która tworzy nowy zasób na serwerze.</span><span class="sxs-lookup"><span data-stu-id="4302f-204">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="4302f-205">`CreatedAtRoute`również dodaje `Location` nagłówek do odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="4302f-205">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="4302f-206">Nagłówek `Location` określa identyfikator URI nowo utworzonej książki.</span><span class="sxs-lookup"><span data-stu-id="4302f-206">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="4302f-207">Testowanie internetowego interfejsu API</span><span class="sxs-lookup"><span data-stu-id="4302f-207">Test the web API</span></span>

1. <span data-ttu-id="4302f-208">Skompiluj i uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="4302f-208">Build and run the app.</span></span>

1. <span data-ttu-id="4302f-209">Przejdź `http://localhost:<port>/api/books` do, aby przetestować `Get` metodę akcji bez parametrów kontrolera.</span><span class="sxs-lookup"><span data-stu-id="4302f-209">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="4302f-210">Wyświetlana jest następująca odpowiedź JSON:</span><span class="sxs-lookup"><span data-stu-id="4302f-210">The following JSON response is displayed:</span></span>

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

1. <span data-ttu-id="4302f-211">Przejdź `http://localhost:<port>/api/books/{id here}` do, aby przetestować metodę `Get` akcji przeciążone kontrolera.</span><span class="sxs-lookup"><span data-stu-id="4302f-211">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="4302f-212">Wyświetlana jest następująca odpowiedź JSON:</span><span class="sxs-lookup"><span data-stu-id="4302f-212">The following JSON response is displayed:</span></span>

    ```json
    {
      "id":"{ID}",
      "bookName":"Clean Code",
      "price":43.15,
      "category":"Computers",
      "author":"Robert C. Martin"
    }
    ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="4302f-213">Konfigurowanie opcji serializacji JSON</span><span class="sxs-lookup"><span data-stu-id="4302f-213">Configure JSON serialization options</span></span>

<span data-ttu-id="4302f-214">Istnieją dwa szczegóły do zmiany dotyczące odpowiedzi JSON zwróconych w sekcji [Testowanie interfejsu API sieci web:](#test-the-web-api)</span><span class="sxs-lookup"><span data-stu-id="4302f-214">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="4302f-215">Domyślna wielkość liter na wielbłądzie nazw właściwości powinna zostać zmieniona tak, aby odpowiadała wielkości liter pascalowej nazw właściwości obiektu CLR.</span><span class="sxs-lookup"><span data-stu-id="4302f-215">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="4302f-216">Właściwość `bookName` powinna zostać `Name`zwrócona jako .</span><span class="sxs-lookup"><span data-stu-id="4302f-216">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="4302f-217">Aby spełnić powyższe wymagania, należy wprowadzić następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="4302f-217">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="4302f-218">JSON.NET został usunięty z ASP.NET udostępnionych ram.</span><span class="sxs-lookup"><span data-stu-id="4302f-218">JSON.NET has been removed from ASP.NET shared framework.</span></span> <span data-ttu-id="4302f-219">Dodaj odwołanie do pakietu [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span><span class="sxs-lookup"><span data-stu-id="4302f-219">Add a package reference to [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span></span>

1. <span data-ttu-id="4302f-220">W `Startup.ConfigureServices`, łańcuch następujący wyróżniony `AddMvc` kod na wywołanie metody:</span><span class="sxs-lookup"><span data-stu-id="4302f-220">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddMvc` method call:</span></span>

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

    <span data-ttu-id="4302f-221">Wraz z poprzednią zmianą nazwy właściwości w serializowanej odpowiedzi JSON interfejsu API sieci web są zgodne z odpowiadającymi im nazwami właściwości w typie obiektu CLR.</span><span class="sxs-lookup"><span data-stu-id="4302f-221">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="4302f-222">Na przykład `Book` `Author` właściwość klasy serializuje `Author`się jako .</span><span class="sxs-lookup"><span data-stu-id="4302f-222">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="4302f-223">W *models/book.cs*, adnotacje `BookName` właściwości [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) z następującym atrybutem:</span><span class="sxs-lookup"><span data-stu-id="4302f-223">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

    ```csharp
    [BsonElement("Name")]
    [JsonProperty("Name")]
    public string BookName { get; set; }
    ```

    <span data-ttu-id="4302f-224">Wartość `[JsonProperty]` atrybutu `Name` reprezentuje nazwę właściwości w serializowanej odpowiedzi JSON interfejsu API sieci web.</span><span class="sxs-lookup"><span data-stu-id="4302f-224">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="4302f-225">Dodaj następujący kod do góry *Models/Book.cs,* aby rozwiązać odwołanie do atrybutu: `[JsonProperty]`</span><span class="sxs-lookup"><span data-stu-id="4302f-225">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

    ```csharp
    using Newtonsoft.Json;
    ```

1. <span data-ttu-id="4302f-226">Powtórz kroki zdefiniowane w sekcji [Testowanie internetowego interfejsu API.](#test-the-web-api)</span><span class="sxs-lookup"><span data-stu-id="4302f-226">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="4302f-227">Zwróć uwagę na różnicę w nazwach właściwości JSON.</span><span class="sxs-lookup"><span data-stu-id="4302f-227">Notice the difference in JSON property names.</span></span>

## <a name="next-steps"></a><span data-ttu-id="4302f-228">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="4302f-228">Next steps</span></span>

<span data-ttu-id="4302f-229">Aby uzyskać więcej informacji na temat tworzenia ASP.NET podstawowych interfejsów API sieci web, zobacz następujące zasoby:</span><span class="sxs-lookup"><span data-stu-id="4302f-229">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="4302f-230">Wersja tego artykułu w YouTube</span><span class="sxs-lookup"><span data-stu-id="4302f-230">YouTube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* [<span data-ttu-id="4302f-231">Tworzenie interfejsów API sieci Web za pomocą ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4302f-231">Create web APIs with ASP.NET Core</span></span>](https://docs.microsoft.com/aspnet/core/web-api/index?view=aspnetcore-3.0)
