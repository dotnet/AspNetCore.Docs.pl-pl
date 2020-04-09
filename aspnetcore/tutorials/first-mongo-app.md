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
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="9d24c-103">Tworzenie internetowego interfejsu API z ASP.NET Core i MongoDB</span><span class="sxs-lookup"><span data-stu-id="9d24c-103">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="9d24c-104">Przez [Pratik Khandelwal](https://twitter.com/K2Prk) i [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="9d24c-104">By [Pratik Khandelwal](https://twitter.com/K2Prk) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9d24c-105">W tym samouczku utworzy się internetowy interfejs API, który wykonuje operacje tworzenia, odczytu, aktualizacji i usuwania (CRUD) w bazie danych [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL.</span><span class="sxs-lookup"><span data-stu-id="9d24c-105">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="9d24c-106">Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="9d24c-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="9d24c-107">Konfigurowanie bazy danych MongoDB</span><span class="sxs-lookup"><span data-stu-id="9d24c-107">Configure MongoDB</span></span>
> * <span data-ttu-id="9d24c-108">Tworzenie bazy danych MongoDB</span><span class="sxs-lookup"><span data-stu-id="9d24c-108">Create a MongoDB database</span></span>
> * <span data-ttu-id="9d24c-109">Definiowanie kolekcji i schematu mongodb</span><span class="sxs-lookup"><span data-stu-id="9d24c-109">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="9d24c-110">Wykonywanie operacji MONGODB CRUD z internetowego interfejsu API</span><span class="sxs-lookup"><span data-stu-id="9d24c-110">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="9d24c-111">Dostosowywanie serializacji JSON</span><span class="sxs-lookup"><span data-stu-id="9d24c-111">Customize JSON serialization</span></span>

<span data-ttu-id="9d24c-112">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9d24c-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9d24c-113">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="9d24c-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9d24c-114">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9d24c-114">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="9d24c-115">Zestaw .NET Core SDK 3.0 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="9d24c-115">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="9d24c-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) z **obciążeniem ASP.NET i tworzenia stron internetowych**</span><span class="sxs-lookup"><span data-stu-id="9d24c-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="9d24c-117">MongoDB</span><span class="sxs-lookup"><span data-stu-id="9d24c-117">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="9d24c-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9d24c-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="9d24c-119">Zestaw .NET Core SDK 3.0 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="9d24c-119">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="9d24c-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9d24c-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="9d24c-121">Rozszerzenie C# for Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9d24c-121">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="9d24c-122">MongoDB</span><span class="sxs-lookup"><span data-stu-id="9d24c-122">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9d24c-123">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="9d24c-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="9d24c-124">Zestaw .NET Core SDK 3.0 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="9d24c-124">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="9d24c-125">Visual Studio dla komputerów Mac w wersji 7.7 lub nowszej</span><span class="sxs-lookup"><span data-stu-id="9d24c-125">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="9d24c-126">MongoDB</span><span class="sxs-lookup"><span data-stu-id="9d24c-126">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="9d24c-127">Konfigurowanie bazy danych MongoDB</span><span class="sxs-lookup"><span data-stu-id="9d24c-127">Configure MongoDB</span></span>

<span data-ttu-id="9d24c-128">Jeśli używasz systemu Windows, MongoDB jest zainstalowany w *C:\\Program Files\\MongoDB* domyślnie.</span><span class="sxs-lookup"><span data-stu-id="9d24c-128">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="9d24c-129">Dodaj *C:\\\\Pliki programów MongoDB\\Server\\\<version_number \\>* `Path` pojemnik do zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="9d24c-129">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="9d24c-130">Ta zmiana umożliwia dostęp MongoDB z dowolnego miejsca na komputerze deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="9d24c-130">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="9d24c-131">Użyj powłoki mongo w poniższych krokach, aby utworzyć bazę danych, utworzyć kolekcje i przechowywać dokumenty.</span><span class="sxs-lookup"><span data-stu-id="9d24c-131">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="9d24c-132">Aby uzyskać więcej informacji na temat poleceń mongo Shell, zobacz [Praca z powłoką mongo](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="9d24c-132">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="9d24c-133">Wybierz katalog na komputerze deweloperskim do przechowywania danych.</span><span class="sxs-lookup"><span data-stu-id="9d24c-133">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="9d24c-134">Na przykład *C:\\BooksData* w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="9d24c-134">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="9d24c-135">Utwórz katalog, jeśli nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="9d24c-135">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="9d24c-136">Mongo Shell nie tworzy nowych katalogów.</span><span class="sxs-lookup"><span data-stu-id="9d24c-136">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="9d24c-137">Otwórz powłokę polecenia.</span><span class="sxs-lookup"><span data-stu-id="9d24c-137">Open a command shell.</span></span> <span data-ttu-id="9d24c-138">Uruchom następujące polecenie, aby połączyć się z mongodb na domyślnym porcie 27017.</span><span class="sxs-lookup"><span data-stu-id="9d24c-138">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="9d24c-139">Pamiętaj, `<data_directory_path>` aby zastąpić katalog wybrany w poprzednim kroku.</span><span class="sxs-lookup"><span data-stu-id="9d24c-139">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="9d24c-140">Otwórz inne wystąpienie powłoki polecenia.</span><span class="sxs-lookup"><span data-stu-id="9d24c-140">Open another command shell instance.</span></span> <span data-ttu-id="9d24c-141">Połącz się z domyślną bazą danych testów, uruchamiając następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="9d24c-141">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="9d24c-142">Uruchom następujące polecenie w powłoce polecenia:</span><span class="sxs-lookup"><span data-stu-id="9d24c-142">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="9d24c-143">Jeśli jeszcze nie istnieje, tworzona jest baza danych o nazwie *BookstoreDb.*</span><span class="sxs-lookup"><span data-stu-id="9d24c-143">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="9d24c-144">Jeśli baza danych istnieje, jej połączenie jest otwierane dla transakcji.</span><span class="sxs-lookup"><span data-stu-id="9d24c-144">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="9d24c-145">Utwórz `Books` kolekcję za pomocą następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="9d24c-145">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="9d24c-146">Wyświetlany jest następujący wynik:</span><span class="sxs-lookup"><span data-stu-id="9d24c-146">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="9d24c-147">Zdefiniuj `Books` schemat kolekcji i wstaw dwa dokumenty za pomocą następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="9d24c-147">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="9d24c-148">Wyświetlany jest następujący wynik:</span><span class="sxs-lookup"><span data-stu-id="9d24c-148">The following result is displayed:</span></span>

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
   > <span data-ttu-id="9d24c-149">Identyfikator jest wyświetlany w tym artykule nie będzie zgodne identyfikatory po uruchomieniu tego przykładu.</span><span class="sxs-lookup"><span data-stu-id="9d24c-149">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="9d24c-150">Wyświetlanie dokumentów w bazie danych za pomocą następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="9d24c-150">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="9d24c-151">Wyświetlany jest następujący wynik:</span><span class="sxs-lookup"><span data-stu-id="9d24c-151">The following result is displayed:</span></span>

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

   <span data-ttu-id="9d24c-152">Schemat dodaje autoryzowaną `_id` właściwość `ObjectId` typu dla każdego dokumentu.</span><span class="sxs-lookup"><span data-stu-id="9d24c-152">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="9d24c-153">Baza danych jest gotowa.</span><span class="sxs-lookup"><span data-stu-id="9d24c-153">The database is ready.</span></span> <span data-ttu-id="9d24c-154">Można rozpocząć tworzenie ASP.NET Core interfejsu API sieci web.</span><span class="sxs-lookup"><span data-stu-id="9d24c-154">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="9d24c-155">Tworzenie projektu internetowego interfejsu API ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9d24c-155">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9d24c-156">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9d24c-156">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="9d24c-157">Przejdź do **pliku** > **nowego** > **projektu**.</span><span class="sxs-lookup"><span data-stu-id="9d24c-157">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="9d24c-158">Wybierz typ projektu **ASP.NET Core Web Application** i wybierz przycisk **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="9d24c-158">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="9d24c-159">Nazwij projekt *BooksApi*i wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="9d24c-159">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="9d24c-160">Wybierz platformę docelową **.NET Core** i **ASP.NET Core 3.0**.</span><span class="sxs-lookup"><span data-stu-id="9d24c-160">Select the **.NET Core** target framework and **ASP.NET Core 3.0**.</span></span> <span data-ttu-id="9d24c-161">Wybierz szablon projektu **interfejsu API** i wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="9d24c-161">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="9d24c-162">Odwiedź [Galerię NuGet: MongoDB.Driver,](https://www.nuget.org/packages/MongoDB.Driver/) aby określić najnowszą stabilną wersję sterownika .NET dla mongodb.</span><span class="sxs-lookup"><span data-stu-id="9d24c-162">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="9d24c-163">W oknie **Konsoli Menedżera pakietów** przejdź do katalogu głównego projektu.</span><span class="sxs-lookup"><span data-stu-id="9d24c-163">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="9d24c-164">Uruchom następujące polecenie, aby zainstalować sterownik .NET dla usługi MongoDB:</span><span class="sxs-lookup"><span data-stu-id="9d24c-164">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="9d24c-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9d24c-165">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="9d24c-166">Uruchom następujące polecenia w powłoce polecenia:</span><span class="sxs-lookup"><span data-stu-id="9d24c-166">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="9d24c-167">Nowy projekt interfejsu API sieci web ASP.NET Core jest generowany i otwierany w programie Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="9d24c-167">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="9d24c-168">Po pasku stanu OmniSharp płomień ikona zmieni kolor na zielony, okno dialogowe pyta **wymagane zasoby do tworzenia i debugowania brakuje "BooksApi". Dodać je?**.</span><span class="sxs-lookup"><span data-stu-id="9d24c-168">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="9d24c-169">Wybierz **pozycję Tak**.</span><span class="sxs-lookup"><span data-stu-id="9d24c-169">Select **Yes**.</span></span>
1. <span data-ttu-id="9d24c-170">Odwiedź [Galerię NuGet: MongoDB.Driver,](https://www.nuget.org/packages/MongoDB.Driver/) aby określić najnowszą stabilną wersję sterownika .NET dla mongodb.</span><span class="sxs-lookup"><span data-stu-id="9d24c-170">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="9d24c-171">Otwórz **zintegrowany terminal** i przejdź do katalogu głównego projektu.</span><span class="sxs-lookup"><span data-stu-id="9d24c-171">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="9d24c-172">Uruchom następujące polecenie, aby zainstalować sterownik .NET dla usługi MongoDB:</span><span class="sxs-lookup"><span data-stu-id="9d24c-172">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9d24c-173">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="9d24c-173">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="9d24c-174">Przejdź do **pliku** > **nowego rozwiązania** > **.NET Core** > **App**.</span><span class="sxs-lookup"><span data-stu-id="9d24c-174">Go to **File** > **New Solution** > **.NET Core** > **App**.</span></span>
1. <span data-ttu-id="9d24c-175">Wybierz szablon projektu **ASP.NET Core Web API** C# i wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="9d24c-175">Select the **ASP.NET Core Web API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="9d24c-176">Z listy rozwijanej **Rama docelowa** wybierz pozycję **.NET Core 3.0,** a następnie wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="9d24c-176">Select **.NET Core 3.0** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="9d24c-177">Wprowadź *pozycję BooksApi* dla **nazwy projektu**i wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="9d24c-177">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="9d24c-178">W konsoli **Rozwiązania** kliknij prawym przyciskiem myszy węzeł **Zależności** projektu i wybierz polecenie **Dodaj pakiety**.</span><span class="sxs-lookup"><span data-stu-id="9d24c-178">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="9d24c-179">Wprowadź *mongoDB.driver* w polu wyszukiwania, wybierz pakiet *MongoDB.Driver* i wybierz **dodaj pakiet**.</span><span class="sxs-lookup"><span data-stu-id="9d24c-179">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="9d24c-180">Wybierz przycisk **Zaakceptuj** w oknie dialogowym **Akceptacja licencji.**</span><span class="sxs-lookup"><span data-stu-id="9d24c-180">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="9d24c-181">Dodawanie modelu encji</span><span class="sxs-lookup"><span data-stu-id="9d24c-181">Add an entity model</span></span>

1. <span data-ttu-id="9d24c-182">Dodaj katalog *Models* do katalogu głównego projektu.</span><span class="sxs-lookup"><span data-stu-id="9d24c-182">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="9d24c-183">Dodaj `Book` klasę do katalogu *Models* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="9d24c-183">Add a `Book` class to the *Models* directory with the following code:</span></span>

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

   <span data-ttu-id="9d24c-184">W poprzedniej klasie `Id` właściwość:</span><span class="sxs-lookup"><span data-stu-id="9d24c-184">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="9d24c-185">Jest wymagane do mapowania obiektu środowiska wykonawczego języka wspólnego (CLR) do kolekcji MongoDB.</span><span class="sxs-lookup"><span data-stu-id="9d24c-185">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="9d24c-186">Jest oś [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) tym adnotacjami, aby wyznaczyć tę właściwość jako klucz podstawowy dokumentu.</span><span class="sxs-lookup"><span data-stu-id="9d24c-186">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="9d24c-187">Jest opisywany [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) z aby umożliwić przekazywanie parametru jako typu `string` zamiast [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) struktury.</span><span class="sxs-lookup"><span data-stu-id="9d24c-187">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="9d24c-188">Mongo obsługuje konwersję `string` `ObjectId`z do .</span><span class="sxs-lookup"><span data-stu-id="9d24c-188">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="9d24c-189">Właściwość `BookName` jest opisywana [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) z atrybutem.</span><span class="sxs-lookup"><span data-stu-id="9d24c-189">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="9d24c-190">Wartość atrybutu `Name` reprezentuje nazwę właściwości w kolekcji MongoDB.</span><span class="sxs-lookup"><span data-stu-id="9d24c-190">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="9d24c-191">Dodawanie modelu konfiguracji</span><span class="sxs-lookup"><span data-stu-id="9d24c-191">Add a configuration model</span></span>

1. <span data-ttu-id="9d24c-192">Dodaj następujące wartości konfiguracji bazy danych do *pliku appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="9d24c-192">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/3.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="9d24c-193">Dodaj plik *BookstoreDatabaseSettings.cs* do katalogu *Models* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="9d24c-193">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="9d24c-194">Poprzednia `BookstoreDatabaseSettings` klasa jest używana do przechowywania wartości `BookstoreDatabaseSettings` właściwości pliku *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="9d24c-194">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="9d24c-195">Nazwy właściwości JSON i C# są nazwane identycznie, aby ułatwić proces mapowania.</span><span class="sxs-lookup"><span data-stu-id="9d24c-195">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="9d24c-196">Dodaj następujący wyróżniony `Startup.ConfigureServices`kod do:</span><span class="sxs-lookup"><span data-stu-id="9d24c-196">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-8)]

   <span data-ttu-id="9d24c-197">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="9d24c-197">In the preceding code:</span></span>

   * <span data-ttu-id="9d24c-198">Wystąpienie konfiguracji, z którym wiąże `BookstoreDatabaseSettings` się sekcja pliku *appsettings.json,* jest zarejestrowane w kontenerze iniekcji zależności (DI).</span><span class="sxs-lookup"><span data-stu-id="9d24c-198">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="9d24c-199">Na przykład `BookstoreDatabaseSettings` właściwość `ConnectionString` obiektu jest wypełniona właściwością `BookstoreDatabaseSettings:ConnectionString` w *pliku appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="9d24c-199">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="9d24c-200">Interfejs `IBookstoreDatabaseSettings` jest zarejestrowany w DI z [okresu istnienia usługi](xref:fundamentals/dependency-injection#service-lifetimes)singleton .</span><span class="sxs-lookup"><span data-stu-id="9d24c-200">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="9d24c-201">Po wstrzyknięciu wystąpienie interfejsu jest `BookstoreDatabaseSettings` rozpoznawane dla obiektu.</span><span class="sxs-lookup"><span data-stu-id="9d24c-201">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="9d24c-202">Dodaj następujący kod do górnej części *Startup.cs,* aby rozwiązać `BookstoreDatabaseSettings` i `IBookstoreDatabaseSettings` odwołania:</span><span class="sxs-lookup"><span data-stu-id="9d24c-202">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="9d24c-203">Dodawanie usługi operacji CRUD</span><span class="sxs-lookup"><span data-stu-id="9d24c-203">Add a CRUD operations service</span></span>

1. <span data-ttu-id="9d24c-204">Dodaj katalog *usług* do katalogu głównego projektu.</span><span class="sxs-lookup"><span data-stu-id="9d24c-204">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="9d24c-205">Dodaj `BookService` klasę do katalogu *usług* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="9d24c-205">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="9d24c-206">W poprzednim kodzie `IBookstoreDatabaseSettings` wystąpienie jest pobierane z DI za pośrednictwem iniekcji konstruktora.</span><span class="sxs-lookup"><span data-stu-id="9d24c-206">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="9d24c-207">Ta technika zapewnia dostęp do wartości konfiguracji *appsettings.json,* które zostały dodane w sekcji [Dodaj model konfiguracji.](#add-a-configuration-model)</span><span class="sxs-lookup"><span data-stu-id="9d24c-207">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="9d24c-208">Dodaj następujący wyróżniony `Startup.ConfigureServices`kod do:</span><span class="sxs-lookup"><span data-stu-id="9d24c-208">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="9d24c-209">W poprzednim kodzie `BookService` klasa jest zarejestrowana w DI do obsługi iniekcji konstruktora w klasach zużywających.</span><span class="sxs-lookup"><span data-stu-id="9d24c-209">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="9d24c-210">Okres istnienia usługi singleton `BookService` jest najbardziej odpowiedni, `MongoClient`ponieważ przyjmuje bezpośrednią zależność od .</span><span class="sxs-lookup"><span data-stu-id="9d24c-210">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="9d24c-211">Zgodnie z [oficjalnymi wytycznymi mongo klienta ponownego wykorzystania,](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use) `MongoClient` powinny być zarejestrowane w DI z okresu istnienia usługi singleton.</span><span class="sxs-lookup"><span data-stu-id="9d24c-211">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="9d24c-212">Dodaj następujący kod do górnej części *Startup.cs,* aby rozwiązać `BookService` odwołanie:</span><span class="sxs-lookup"><span data-stu-id="9d24c-212">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="9d24c-213">Klasa `BookService` używa następujących `MongoDB.Driver` elementów członkowskich do wykonywania operacji CRUD w bazie danych:</span><span class="sxs-lookup"><span data-stu-id="9d24c-213">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="9d24c-214">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; odczytuje wystąpienie serwera do wykonywania operacji bazy danych.</span><span class="sxs-lookup"><span data-stu-id="9d24c-214">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Reads the server instance for performing database operations.</span></span> <span data-ttu-id="9d24c-215">Konstruktor tej klasy jest pod warunkiem parametry połączenia MongoDB:</span><span class="sxs-lookup"><span data-stu-id="9d24c-215">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="9d24c-216">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Reprezentuje mongo bazy danych do wykonywania operacji.</span><span class="sxs-lookup"><span data-stu-id="9d24c-216">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="9d24c-217">W tym samouczku użyto ogólnej metody [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) w interfejsie, aby uzyskać dostęp do danych w określonej kolekcji.</span><span class="sxs-lookup"><span data-stu-id="9d24c-217">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="9d24c-218">Wykonaj operacje CRUD względem kolekcji po wywołaniu tej metody.</span><span class="sxs-lookup"><span data-stu-id="9d24c-218">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="9d24c-219">W `GetCollection<TDocument>(collection)` wywołaniu metody:</span><span class="sxs-lookup"><span data-stu-id="9d24c-219">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="9d24c-220">`collection`reprezentuje nazwę kolekcji.</span><span class="sxs-lookup"><span data-stu-id="9d24c-220">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="9d24c-221">`TDocument`reprezentuje typ obiektu CLR przechowywane w kolekcji.</span><span class="sxs-lookup"><span data-stu-id="9d24c-221">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="9d24c-222">`GetCollection<TDocument>(collection)`zwraca [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) obiektu reprezentującego kolekcję.</span><span class="sxs-lookup"><span data-stu-id="9d24c-222">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="9d24c-223">W tym samouczku są wywoływane w kolekcji następujące metody:</span><span class="sxs-lookup"><span data-stu-id="9d24c-223">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="9d24c-224">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Usuwa pojedynczy dokument spełniający podane kryteria wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="9d24c-224">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="9d24c-225">[Znajdź\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Zwraca wszystkie dokumenty w kolekcji zgodne z podanymi kryteriami wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="9d24c-225">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="9d24c-226">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Wstawia podany obiekt jako nowy dokument w kolekcji.</span><span class="sxs-lookup"><span data-stu-id="9d24c-226">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="9d24c-227">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Zastępuje pojedynczy dokument pasujący do podanych kryteriów wyszukiwania z podanym obiektem.</span><span class="sxs-lookup"><span data-stu-id="9d24c-227">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="9d24c-228">Dodawanie kontrolera</span><span class="sxs-lookup"><span data-stu-id="9d24c-228">Add a controller</span></span>

<span data-ttu-id="9d24c-229">Dodaj `BooksController` klasę do katalogu *Controllers* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="9d24c-229">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="9d24c-230">Poprzedni kontroler interfejsu API sieci web:</span><span class="sxs-lookup"><span data-stu-id="9d24c-230">The preceding web API controller:</span></span>

* <span data-ttu-id="9d24c-231">Używa `BookService` klasy do wykonywania operacji CRUD.</span><span class="sxs-lookup"><span data-stu-id="9d24c-231">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="9d24c-232">Zawiera metody akcji do obsługi żądań HTTP GET, POST, PUT i DELETE.</span><span class="sxs-lookup"><span data-stu-id="9d24c-232">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="9d24c-233">Wywołania <xref:System.Web.Http.ApiController.CreatedAtRoute*> `Create` w metodzie akcji, aby zwrócić odpowiedź [HTTP 201.](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)</span><span class="sxs-lookup"><span data-stu-id="9d24c-233">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="9d24c-234">Kod stanu 201 jest standardową odpowiedzią dla metody HTTP POST, która tworzy nowy zasób na serwerze.</span><span class="sxs-lookup"><span data-stu-id="9d24c-234">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="9d24c-235">`CreatedAtRoute`również dodaje `Location` nagłówek do odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="9d24c-235">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="9d24c-236">Nagłówek `Location` określa identyfikator URI nowo utworzonej książki.</span><span class="sxs-lookup"><span data-stu-id="9d24c-236">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="9d24c-237">Testowanie internetowego interfejsu API</span><span class="sxs-lookup"><span data-stu-id="9d24c-237">Test the web API</span></span>

1. <span data-ttu-id="9d24c-238">Skompiluj i uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="9d24c-238">Build and run the app.</span></span>

1. <span data-ttu-id="9d24c-239">Przejdź `http://localhost:<port>/api/books` do, aby przetestować `Get` metodę akcji bez parametrów kontrolera.</span><span class="sxs-lookup"><span data-stu-id="9d24c-239">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="9d24c-240">Wyświetlana jest następująca odpowiedź JSON:</span><span class="sxs-lookup"><span data-stu-id="9d24c-240">The following JSON response is displayed:</span></span>

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

1. <span data-ttu-id="9d24c-241">Przejdź `http://localhost:<port>/api/books/{id here}` do, aby przetestować metodę `Get` akcji przeciążone kontrolera.</span><span class="sxs-lookup"><span data-stu-id="9d24c-241">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="9d24c-242">Wyświetlana jest następująca odpowiedź JSON:</span><span class="sxs-lookup"><span data-stu-id="9d24c-242">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="9d24c-243">Konfigurowanie opcji serializacji JSON</span><span class="sxs-lookup"><span data-stu-id="9d24c-243">Configure JSON serialization options</span></span>

<span data-ttu-id="9d24c-244">Istnieją dwa szczegóły do zmiany dotyczące odpowiedzi JSON zwróconych w sekcji [Testowanie interfejsu API sieci web:](#test-the-web-api)</span><span class="sxs-lookup"><span data-stu-id="9d24c-244">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="9d24c-245">Domyślna wielkość liter na wielbłądzie nazw właściwości powinna zostać zmieniona tak, aby odpowiadała wielkości liter pascalowej nazw właściwości obiektu CLR.</span><span class="sxs-lookup"><span data-stu-id="9d24c-245">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="9d24c-246">Właściwość `bookName` powinna zostać `Name`zwrócona jako .</span><span class="sxs-lookup"><span data-stu-id="9d24c-246">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="9d24c-247">Aby spełnić powyższe wymagania, należy wprowadzić następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="9d24c-247">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="9d24c-248">JSON.NET został usunięty z ASP.NET udostępnionych ram.</span><span class="sxs-lookup"><span data-stu-id="9d24c-248">JSON.NET has been removed from ASP.NET shared framework.</span></span> <span data-ttu-id="9d24c-249">Dodaj odwołanie do pakietu [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span><span class="sxs-lookup"><span data-stu-id="9d24c-249">Add a package reference to [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span></span>

1. <span data-ttu-id="9d24c-250">W `Startup.ConfigureServices`, łańcuch następujący wyróżniony `AddControllers` kod na wywołanie metody:</span><span class="sxs-lookup"><span data-stu-id="9d24c-250">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddControllers` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="9d24c-251">Wraz z poprzednią zmianą nazwy właściwości w serializowanej odpowiedzi JSON interfejsu API sieci web są zgodne z odpowiadającymi im nazwami właściwości w typie obiektu CLR.</span><span class="sxs-lookup"><span data-stu-id="9d24c-251">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="9d24c-252">Na przykład `Book` `Author` właściwość klasy serializuje `Author`się jako .</span><span class="sxs-lookup"><span data-stu-id="9d24c-252">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="9d24c-253">W *models/book.cs*, adnotacje `BookName` właściwości [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) z następującym atrybutem:</span><span class="sxs-lookup"><span data-stu-id="9d24c-253">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="9d24c-254">Wartość `[JsonProperty]` atrybutu `Name` reprezentuje nazwę właściwości w serializowanej odpowiedzi JSON interfejsu API sieci web.</span><span class="sxs-lookup"><span data-stu-id="9d24c-254">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="9d24c-255">Dodaj następujący kod do góry *Models/Book.cs,* aby rozwiązać odwołanie do atrybutu: `[JsonProperty]`</span><span class="sxs-lookup"><span data-stu-id="9d24c-255">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="9d24c-256">Powtórz kroki zdefiniowane w sekcji [Testowanie internetowego interfejsu API.](#test-the-web-api)</span><span class="sxs-lookup"><span data-stu-id="9d24c-256">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="9d24c-257">Zwróć uwagę na różnicę w nazwach właściwości JSON.</span><span class="sxs-lookup"><span data-stu-id="9d24c-257">Notice the difference in JSON property names.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9d24c-258">W tym samouczku utworzy się internetowy interfejs API, który wykonuje operacje tworzenia, odczytu, aktualizacji i usuwania (CRUD) w bazie danych [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL.</span><span class="sxs-lookup"><span data-stu-id="9d24c-258">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="9d24c-259">Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="9d24c-259">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="9d24c-260">Konfigurowanie bazy danych MongoDB</span><span class="sxs-lookup"><span data-stu-id="9d24c-260">Configure MongoDB</span></span>
> * <span data-ttu-id="9d24c-261">Tworzenie bazy danych MongoDB</span><span class="sxs-lookup"><span data-stu-id="9d24c-261">Create a MongoDB database</span></span>
> * <span data-ttu-id="9d24c-262">Definiowanie kolekcji i schematu mongodb</span><span class="sxs-lookup"><span data-stu-id="9d24c-262">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="9d24c-263">Wykonywanie operacji MONGODB CRUD z internetowego interfejsu API</span><span class="sxs-lookup"><span data-stu-id="9d24c-263">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="9d24c-264">Dostosowywanie serializacji JSON</span><span class="sxs-lookup"><span data-stu-id="9d24c-264">Customize JSON serialization</span></span>

<span data-ttu-id="9d24c-265">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9d24c-265">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9d24c-266">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="9d24c-266">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9d24c-267">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9d24c-267">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="9d24c-268">.NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="9d24c-268">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="9d24c-269">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) z **obciążeniem ASP.NET i tworzenia stron internetowych**</span><span class="sxs-lookup"><span data-stu-id="9d24c-269">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="9d24c-270">MongoDB</span><span class="sxs-lookup"><span data-stu-id="9d24c-270">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="9d24c-271">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9d24c-271">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="9d24c-272">.NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="9d24c-272">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="9d24c-273">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9d24c-273">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="9d24c-274">Rozszerzenie C# for Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9d24c-274">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="9d24c-275">MongoDB</span><span class="sxs-lookup"><span data-stu-id="9d24c-275">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9d24c-276">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="9d24c-276">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="9d24c-277">.NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="9d24c-277">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="9d24c-278">Visual Studio dla komputerów Mac w wersji 7.7 lub nowszej</span><span class="sxs-lookup"><span data-stu-id="9d24c-278">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="9d24c-279">MongoDB</span><span class="sxs-lookup"><span data-stu-id="9d24c-279">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="9d24c-280">Konfigurowanie bazy danych MongoDB</span><span class="sxs-lookup"><span data-stu-id="9d24c-280">Configure MongoDB</span></span>

<span data-ttu-id="9d24c-281">Jeśli używasz systemu Windows, MongoDB jest zainstalowany w *C:\\Program Files\\MongoDB* domyślnie.</span><span class="sxs-lookup"><span data-stu-id="9d24c-281">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="9d24c-282">Dodaj *C:\\\\Pliki programów MongoDB\\Server\\\<version_number \\>* `Path` pojemnik do zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="9d24c-282">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="9d24c-283">Ta zmiana umożliwia dostęp MongoDB z dowolnego miejsca na komputerze deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="9d24c-283">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="9d24c-284">Użyj powłoki mongo w poniższych krokach, aby utworzyć bazę danych, utworzyć kolekcje i przechowywać dokumenty.</span><span class="sxs-lookup"><span data-stu-id="9d24c-284">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="9d24c-285">Aby uzyskać więcej informacji na temat poleceń mongo Shell, zobacz [Praca z powłoką mongo](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="9d24c-285">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="9d24c-286">Wybierz katalog na komputerze deweloperskim do przechowywania danych.</span><span class="sxs-lookup"><span data-stu-id="9d24c-286">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="9d24c-287">Na przykład *C:\\BooksData* w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="9d24c-287">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="9d24c-288">Utwórz katalog, jeśli nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="9d24c-288">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="9d24c-289">Mongo Shell nie tworzy nowych katalogów.</span><span class="sxs-lookup"><span data-stu-id="9d24c-289">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="9d24c-290">Otwórz powłokę polecenia.</span><span class="sxs-lookup"><span data-stu-id="9d24c-290">Open a command shell.</span></span> <span data-ttu-id="9d24c-291">Uruchom następujące polecenie, aby połączyć się z mongodb na domyślnym porcie 27017.</span><span class="sxs-lookup"><span data-stu-id="9d24c-291">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="9d24c-292">Pamiętaj, `<data_directory_path>` aby zastąpić katalog wybrany w poprzednim kroku.</span><span class="sxs-lookup"><span data-stu-id="9d24c-292">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="9d24c-293">Otwórz inne wystąpienie powłoki polecenia.</span><span class="sxs-lookup"><span data-stu-id="9d24c-293">Open another command shell instance.</span></span> <span data-ttu-id="9d24c-294">Połącz się z domyślną bazą danych testów, uruchamiając następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="9d24c-294">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="9d24c-295">Uruchom następujące polecenie w powłoce polecenia:</span><span class="sxs-lookup"><span data-stu-id="9d24c-295">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="9d24c-296">Jeśli jeszcze nie istnieje, tworzona jest baza danych o nazwie *BookstoreDb.*</span><span class="sxs-lookup"><span data-stu-id="9d24c-296">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="9d24c-297">Jeśli baza danych istnieje, jej połączenie jest otwierane dla transakcji.</span><span class="sxs-lookup"><span data-stu-id="9d24c-297">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="9d24c-298">Utwórz `Books` kolekcję za pomocą następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="9d24c-298">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="9d24c-299">Wyświetlany jest następujący wynik:</span><span class="sxs-lookup"><span data-stu-id="9d24c-299">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="9d24c-300">Zdefiniuj `Books` schemat kolekcji i wstaw dwa dokumenty za pomocą następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="9d24c-300">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="9d24c-301">Wyświetlany jest następujący wynik:</span><span class="sxs-lookup"><span data-stu-id="9d24c-301">The following result is displayed:</span></span>

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
   > <span data-ttu-id="9d24c-302">Identyfikator jest wyświetlany w tym artykule nie będzie zgodne identyfikatory po uruchomieniu tego przykładu.</span><span class="sxs-lookup"><span data-stu-id="9d24c-302">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="9d24c-303">Wyświetlanie dokumentów w bazie danych za pomocą następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="9d24c-303">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="9d24c-304">Wyświetlany jest następujący wynik:</span><span class="sxs-lookup"><span data-stu-id="9d24c-304">The following result is displayed:</span></span>

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

   <span data-ttu-id="9d24c-305">Schemat dodaje autoryzowaną `_id` właściwość `ObjectId` typu dla każdego dokumentu.</span><span class="sxs-lookup"><span data-stu-id="9d24c-305">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="9d24c-306">Baza danych jest gotowa.</span><span class="sxs-lookup"><span data-stu-id="9d24c-306">The database is ready.</span></span> <span data-ttu-id="9d24c-307">Można rozpocząć tworzenie ASP.NET Core interfejsu API sieci web.</span><span class="sxs-lookup"><span data-stu-id="9d24c-307">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="9d24c-308">Tworzenie projektu internetowego interfejsu API ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9d24c-308">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9d24c-309">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9d24c-309">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="9d24c-310">Przejdź do **pliku** > **nowego** > **projektu**.</span><span class="sxs-lookup"><span data-stu-id="9d24c-310">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="9d24c-311">Wybierz typ projektu **ASP.NET Core Web Application** i wybierz przycisk **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="9d24c-311">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="9d24c-312">Nazwij projekt *BooksApi*i wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="9d24c-312">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="9d24c-313">Wybierz platformę docelową **.NET Core** i **ASP.NET Core 2.2**.</span><span class="sxs-lookup"><span data-stu-id="9d24c-313">Select the **.NET Core** target framework and **ASP.NET Core 2.2**.</span></span> <span data-ttu-id="9d24c-314">Wybierz szablon projektu **interfejsu API** i wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="9d24c-314">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="9d24c-315">Odwiedź [Galerię NuGet: MongoDB.Driver,](https://www.nuget.org/packages/MongoDB.Driver/) aby określić najnowszą stabilną wersję sterownika .NET dla mongodb.</span><span class="sxs-lookup"><span data-stu-id="9d24c-315">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="9d24c-316">W oknie **Konsoli Menedżera pakietów** przejdź do katalogu głównego projektu.</span><span class="sxs-lookup"><span data-stu-id="9d24c-316">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="9d24c-317">Uruchom następujące polecenie, aby zainstalować sterownik .NET dla usługi MongoDB:</span><span class="sxs-lookup"><span data-stu-id="9d24c-317">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="9d24c-318">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9d24c-318">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="9d24c-319">Uruchom następujące polecenia w powłoce polecenia:</span><span class="sxs-lookup"><span data-stu-id="9d24c-319">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="9d24c-320">Nowy projekt interfejsu API sieci web ASP.NET Core jest generowany i otwierany w programie Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="9d24c-320">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="9d24c-321">Po pasku stanu OmniSharp płomień ikona zmieni kolor na zielony, okno dialogowe pyta **wymagane zasoby do tworzenia i debugowania brakuje "BooksApi". Dodać je?**.</span><span class="sxs-lookup"><span data-stu-id="9d24c-321">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="9d24c-322">Wybierz **pozycję Tak**.</span><span class="sxs-lookup"><span data-stu-id="9d24c-322">Select **Yes**.</span></span>
1. <span data-ttu-id="9d24c-323">Odwiedź [Galerię NuGet: MongoDB.Driver,](https://www.nuget.org/packages/MongoDB.Driver/) aby określić najnowszą stabilną wersję sterownika .NET dla mongodb.</span><span class="sxs-lookup"><span data-stu-id="9d24c-323">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="9d24c-324">Otwórz **zintegrowany terminal** i przejdź do katalogu głównego projektu.</span><span class="sxs-lookup"><span data-stu-id="9d24c-324">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="9d24c-325">Uruchom następujące polecenie, aby zainstalować sterownik .NET dla usługi MongoDB:</span><span class="sxs-lookup"><span data-stu-id="9d24c-325">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9d24c-326">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="9d24c-326">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="9d24c-327">Przejdź do **pliku** > **nowego rozwiązania** > **.NET Core** > **App**.</span><span class="sxs-lookup"><span data-stu-id="9d24c-327">Go to **File** > **New Solution** > **.NET Core** > **App**.</span></span>
1. <span data-ttu-id="9d24c-328">Wybierz szablon projektu **ASP.NET Core Web API** C# i wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="9d24c-328">Select the **ASP.NET Core Web API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="9d24c-329">Z listy rozwijanej **"Struktura docelowa"** wybierz pozycję **.NET Core 2.2,** a następnie wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="9d24c-329">Select **.NET Core 2.2** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="9d24c-330">Wprowadź *pozycję BooksApi* dla **nazwy projektu**i wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="9d24c-330">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="9d24c-331">W konsoli **Rozwiązania** kliknij prawym przyciskiem myszy węzeł **Zależności** projektu i wybierz polecenie **Dodaj pakiety**.</span><span class="sxs-lookup"><span data-stu-id="9d24c-331">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="9d24c-332">Wprowadź *mongoDB.driver* w polu wyszukiwania, wybierz pakiet *MongoDB.Driver* i wybierz **dodaj pakiet**.</span><span class="sxs-lookup"><span data-stu-id="9d24c-332">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="9d24c-333">Wybierz przycisk **Zaakceptuj** w oknie dialogowym **Akceptacja licencji.**</span><span class="sxs-lookup"><span data-stu-id="9d24c-333">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="9d24c-334">Dodawanie modelu encji</span><span class="sxs-lookup"><span data-stu-id="9d24c-334">Add an entity model</span></span>

1. <span data-ttu-id="9d24c-335">Dodaj katalog *Models* do katalogu głównego projektu.</span><span class="sxs-lookup"><span data-stu-id="9d24c-335">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="9d24c-336">Dodaj `Book` klasę do katalogu *Models* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="9d24c-336">Add a `Book` class to the *Models* directory with the following code:</span></span>

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

   <span data-ttu-id="9d24c-337">W poprzedniej klasie `Id` właściwość:</span><span class="sxs-lookup"><span data-stu-id="9d24c-337">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="9d24c-338">Jest wymagane do mapowania obiektu środowiska wykonawczego języka wspólnego (CLR) do kolekcji MongoDB.</span><span class="sxs-lookup"><span data-stu-id="9d24c-338">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="9d24c-339">Jest oś [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) tym adnotacjami, aby wyznaczyć tę właściwość jako klucz podstawowy dokumentu.</span><span class="sxs-lookup"><span data-stu-id="9d24c-339">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="9d24c-340">Jest opisywany [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) z aby umożliwić przekazywanie parametru jako typu `string` zamiast [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) struktury.</span><span class="sxs-lookup"><span data-stu-id="9d24c-340">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="9d24c-341">Mongo obsługuje konwersję `string` `ObjectId`z do .</span><span class="sxs-lookup"><span data-stu-id="9d24c-341">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="9d24c-342">Właściwość `BookName` jest opisywana [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) z atrybutem.</span><span class="sxs-lookup"><span data-stu-id="9d24c-342">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="9d24c-343">Wartość atrybutu `Name` reprezentuje nazwę właściwości w kolekcji MongoDB.</span><span class="sxs-lookup"><span data-stu-id="9d24c-343">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="9d24c-344">Dodawanie modelu konfiguracji</span><span class="sxs-lookup"><span data-stu-id="9d24c-344">Add a configuration model</span></span>

1. <span data-ttu-id="9d24c-345">Dodaj następujące wartości konfiguracji bazy danych do *pliku appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="9d24c-345">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/2.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="9d24c-346">Dodaj plik *BookstoreDatabaseSettings.cs* do katalogu *Models* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="9d24c-346">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="9d24c-347">Poprzednia `BookstoreDatabaseSettings` klasa jest używana do przechowywania wartości `BookstoreDatabaseSettings` właściwości pliku *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="9d24c-347">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="9d24c-348">Nazwy właściwości JSON i C# są nazwane identycznie, aby ułatwić proces mapowania.</span><span class="sxs-lookup"><span data-stu-id="9d24c-348">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="9d24c-349">Dodaj następujący wyróżniony `Startup.ConfigureServices`kod do:</span><span class="sxs-lookup"><span data-stu-id="9d24c-349">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   <span data-ttu-id="9d24c-350">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="9d24c-350">In the preceding code:</span></span>

   * <span data-ttu-id="9d24c-351">Wystąpienie konfiguracji, z którym wiąże `BookstoreDatabaseSettings` się sekcja pliku *appsettings.json,* jest zarejestrowane w kontenerze iniekcji zależności (DI).</span><span class="sxs-lookup"><span data-stu-id="9d24c-351">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="9d24c-352">Na przykład `BookstoreDatabaseSettings` właściwość `ConnectionString` obiektu jest wypełniona właściwością `BookstoreDatabaseSettings:ConnectionString` w *pliku appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="9d24c-352">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="9d24c-353">Interfejs `IBookstoreDatabaseSettings` jest zarejestrowany w DI z [okresu istnienia usługi](xref:fundamentals/dependency-injection#service-lifetimes)singleton .</span><span class="sxs-lookup"><span data-stu-id="9d24c-353">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="9d24c-354">Po wstrzyknięciu wystąpienie interfejsu jest `BookstoreDatabaseSettings` rozpoznawane dla obiektu.</span><span class="sxs-lookup"><span data-stu-id="9d24c-354">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="9d24c-355">Dodaj następujący kod do górnej części *Startup.cs,* aby rozwiązać `BookstoreDatabaseSettings` i `IBookstoreDatabaseSettings` odwołania:</span><span class="sxs-lookup"><span data-stu-id="9d24c-355">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="9d24c-356">Dodawanie usługi operacji CRUD</span><span class="sxs-lookup"><span data-stu-id="9d24c-356">Add a CRUD operations service</span></span>

1. <span data-ttu-id="9d24c-357">Dodaj katalog *usług* do katalogu głównego projektu.</span><span class="sxs-lookup"><span data-stu-id="9d24c-357">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="9d24c-358">Dodaj `BookService` klasę do katalogu *usług* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="9d24c-358">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="9d24c-359">W poprzednim kodzie `IBookstoreDatabaseSettings` wystąpienie jest pobierane z DI za pośrednictwem iniekcji konstruktora.</span><span class="sxs-lookup"><span data-stu-id="9d24c-359">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="9d24c-360">Ta technika zapewnia dostęp do wartości konfiguracji *appsettings.json,* które zostały dodane w sekcji [Dodaj model konfiguracji.](#add-a-configuration-model)</span><span class="sxs-lookup"><span data-stu-id="9d24c-360">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="9d24c-361">Dodaj następujący wyróżniony `Startup.ConfigureServices`kod do:</span><span class="sxs-lookup"><span data-stu-id="9d24c-361">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="9d24c-362">W poprzednim kodzie `BookService` klasa jest zarejestrowana w DI do obsługi iniekcji konstruktora w klasach zużywających.</span><span class="sxs-lookup"><span data-stu-id="9d24c-362">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="9d24c-363">Okres istnienia usługi singleton `BookService` jest najbardziej odpowiedni, `MongoClient`ponieważ przyjmuje bezpośrednią zależność od .</span><span class="sxs-lookup"><span data-stu-id="9d24c-363">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="9d24c-364">Zgodnie z [oficjalnymi wytycznymi mongo klienta ponownego wykorzystania,](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use) `MongoClient` powinny być zarejestrowane w DI z okresu istnienia usługi singleton.</span><span class="sxs-lookup"><span data-stu-id="9d24c-364">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="9d24c-365">Dodaj następujący kod do górnej części *Startup.cs,* aby rozwiązać `BookService` odwołanie:</span><span class="sxs-lookup"><span data-stu-id="9d24c-365">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="9d24c-366">Klasa `BookService` używa następujących `MongoDB.Driver` elementów członkowskich do wykonywania operacji CRUD w bazie danych:</span><span class="sxs-lookup"><span data-stu-id="9d24c-366">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="9d24c-367">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; odczytuje wystąpienie serwera do wykonywania operacji bazy danych.</span><span class="sxs-lookup"><span data-stu-id="9d24c-367">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Reads the server instance for performing database operations.</span></span> <span data-ttu-id="9d24c-368">Konstruktor tej klasy jest pod warunkiem parametry połączenia MongoDB:</span><span class="sxs-lookup"><span data-stu-id="9d24c-368">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="9d24c-369">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Reprezentuje mongo bazy danych do wykonywania operacji.</span><span class="sxs-lookup"><span data-stu-id="9d24c-369">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="9d24c-370">W tym samouczku użyto ogólnej metody [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) w interfejsie, aby uzyskać dostęp do danych w określonej kolekcji.</span><span class="sxs-lookup"><span data-stu-id="9d24c-370">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="9d24c-371">Wykonaj operacje CRUD względem kolekcji po wywołaniu tej metody.</span><span class="sxs-lookup"><span data-stu-id="9d24c-371">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="9d24c-372">W `GetCollection<TDocument>(collection)` wywołaniu metody:</span><span class="sxs-lookup"><span data-stu-id="9d24c-372">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="9d24c-373">`collection`reprezentuje nazwę kolekcji.</span><span class="sxs-lookup"><span data-stu-id="9d24c-373">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="9d24c-374">`TDocument`reprezentuje typ obiektu CLR przechowywane w kolekcji.</span><span class="sxs-lookup"><span data-stu-id="9d24c-374">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="9d24c-375">`GetCollection<TDocument>(collection)`zwraca [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) obiektu reprezentującego kolekcję.</span><span class="sxs-lookup"><span data-stu-id="9d24c-375">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="9d24c-376">W tym samouczku są wywoływane w kolekcji następujące metody:</span><span class="sxs-lookup"><span data-stu-id="9d24c-376">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="9d24c-377">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Usuwa pojedynczy dokument spełniający podane kryteria wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="9d24c-377">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="9d24c-378">[Znajdź\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Zwraca wszystkie dokumenty w kolekcji zgodne z podanymi kryteriami wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="9d24c-378">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="9d24c-379">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Wstawia podany obiekt jako nowy dokument w kolekcji.</span><span class="sxs-lookup"><span data-stu-id="9d24c-379">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="9d24c-380">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Zastępuje pojedynczy dokument pasujący do podanych kryteriów wyszukiwania z podanym obiektem.</span><span class="sxs-lookup"><span data-stu-id="9d24c-380">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="9d24c-381">Dodawanie kontrolera</span><span class="sxs-lookup"><span data-stu-id="9d24c-381">Add a controller</span></span>

<span data-ttu-id="9d24c-382">Dodaj `BooksController` klasę do katalogu *Controllers* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="9d24c-382">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="9d24c-383">Poprzedni kontroler interfejsu API sieci web:</span><span class="sxs-lookup"><span data-stu-id="9d24c-383">The preceding web API controller:</span></span>

* <span data-ttu-id="9d24c-384">Używa `BookService` klasy do wykonywania operacji CRUD.</span><span class="sxs-lookup"><span data-stu-id="9d24c-384">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="9d24c-385">Zawiera metody akcji do obsługi żądań HTTP GET, POST, PUT i DELETE.</span><span class="sxs-lookup"><span data-stu-id="9d24c-385">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="9d24c-386">Wywołania <xref:System.Web.Http.ApiController.CreatedAtRoute*> `Create` w metodzie akcji, aby zwrócić odpowiedź [HTTP 201.](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)</span><span class="sxs-lookup"><span data-stu-id="9d24c-386">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="9d24c-387">Kod stanu 201 jest standardową odpowiedzią dla metody HTTP POST, która tworzy nowy zasób na serwerze.</span><span class="sxs-lookup"><span data-stu-id="9d24c-387">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="9d24c-388">`CreatedAtRoute`również dodaje `Location` nagłówek do odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="9d24c-388">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="9d24c-389">Nagłówek `Location` określa identyfikator URI nowo utworzonej książki.</span><span class="sxs-lookup"><span data-stu-id="9d24c-389">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="9d24c-390">Testowanie internetowego interfejsu API</span><span class="sxs-lookup"><span data-stu-id="9d24c-390">Test the web API</span></span>

1. <span data-ttu-id="9d24c-391">Skompiluj i uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="9d24c-391">Build and run the app.</span></span>

1. <span data-ttu-id="9d24c-392">Przejdź `http://localhost:<port>/api/books` do, aby przetestować `Get` metodę akcji bez parametrów kontrolera.</span><span class="sxs-lookup"><span data-stu-id="9d24c-392">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="9d24c-393">Wyświetlana jest następująca odpowiedź JSON:</span><span class="sxs-lookup"><span data-stu-id="9d24c-393">The following JSON response is displayed:</span></span>

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

1. <span data-ttu-id="9d24c-394">Przejdź `http://localhost:<port>/api/books/{id here}` do, aby przetestować metodę `Get` akcji przeciążone kontrolera.</span><span class="sxs-lookup"><span data-stu-id="9d24c-394">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="9d24c-395">Wyświetlana jest następująca odpowiedź JSON:</span><span class="sxs-lookup"><span data-stu-id="9d24c-395">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="9d24c-396">Konfigurowanie opcji serializacji JSON</span><span class="sxs-lookup"><span data-stu-id="9d24c-396">Configure JSON serialization options</span></span>

<span data-ttu-id="9d24c-397">Istnieją dwa szczegóły do zmiany dotyczące odpowiedzi JSON zwróconych w sekcji [Testowanie interfejsu API sieci web:](#test-the-web-api)</span><span class="sxs-lookup"><span data-stu-id="9d24c-397">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="9d24c-398">Domyślna wielkość liter na wielbłądzie nazw właściwości powinna zostać zmieniona tak, aby odpowiadała wielkości liter pascalowej nazw właściwości obiektu CLR.</span><span class="sxs-lookup"><span data-stu-id="9d24c-398">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="9d24c-399">Właściwość `bookName` powinna zostać `Name`zwrócona jako .</span><span class="sxs-lookup"><span data-stu-id="9d24c-399">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="9d24c-400">Aby spełnić powyższe wymagania, należy wprowadzić następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="9d24c-400">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="9d24c-401">W `Startup.ConfigureServices`, łańcuch następujący wyróżniony `AddMvc` kod na wywołanie metody:</span><span class="sxs-lookup"><span data-stu-id="9d24c-401">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddMvc` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="9d24c-402">Wraz z poprzednią zmianą nazwy właściwości w serializowanej odpowiedzi JSON interfejsu API sieci web są zgodne z odpowiadającymi im nazwami właściwości w typie obiektu CLR.</span><span class="sxs-lookup"><span data-stu-id="9d24c-402">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="9d24c-403">Na przykład `Book` `Author` właściwość klasy serializuje `Author`się jako .</span><span class="sxs-lookup"><span data-stu-id="9d24c-403">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="9d24c-404">W *models/book.cs*, adnotacje `BookName` właściwości [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) z następującym atrybutem:</span><span class="sxs-lookup"><span data-stu-id="9d24c-404">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="9d24c-405">Wartość `[JsonProperty]` atrybutu `Name` reprezentuje nazwę właściwości w serializowanej odpowiedzi JSON interfejsu API sieci web.</span><span class="sxs-lookup"><span data-stu-id="9d24c-405">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="9d24c-406">Dodaj następujący kod do góry *Models/Book.cs,* aby rozwiązać odwołanie do atrybutu: `[JsonProperty]`</span><span class="sxs-lookup"><span data-stu-id="9d24c-406">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="9d24c-407">Powtórz kroki zdefiniowane w sekcji [Testowanie internetowego interfejsu API.](#test-the-web-api)</span><span class="sxs-lookup"><span data-stu-id="9d24c-407">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="9d24c-408">Zwróć uwagę na różnicę w nazwach właściwości JSON.</span><span class="sxs-lookup"><span data-stu-id="9d24c-408">Notice the difference in JSON property names.</span></span>

::: moniker-end

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="9d24c-409">Dodawanie obsługi uwierzytelniania do internetowego interfejsu API</span><span class="sxs-lookup"><span data-stu-id="9d24c-409">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="next-steps"></a><span data-ttu-id="9d24c-410">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="9d24c-410">Next steps</span></span>

<span data-ttu-id="9d24c-411">Aby uzyskać więcej informacji na temat tworzenia ASP.NET podstawowych interfejsów API sieci web, zobacz następujące zasoby:</span><span class="sxs-lookup"><span data-stu-id="9d24c-411">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="9d24c-412">Wersja tego artykułu w YouTube</span><span class="sxs-lookup"><span data-stu-id="9d24c-412">YouTube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
