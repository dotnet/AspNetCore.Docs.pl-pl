---
title: Tworzenie internetowego interfejsu API za pomocą ASP.NET Core i MongoDB
author: prkhandelwal
description: W tym samouczku przedstawiono sposób tworzenia ASP.NET Core internetowego interfejsu API przy użyciu bazy danych NoSQL MongoDB.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 08/17/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mongo-app
ms.openlocfilehash: 4d1c2d915c646dd1c8fcadd25bcd420a0a749dc9
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774772"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="6ad73-103">Tworzenie internetowego interfejsu API za pomocą ASP.NET Core i MongoDB</span><span class="sxs-lookup"><span data-stu-id="6ad73-103">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="6ad73-104">Autorzy [Pratik Khandelwal](https://twitter.com/K2Prk) i [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="6ad73-104">By [Pratik Khandelwal](https://twitter.com/K2Prk) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6ad73-105">W tym samouczku przedstawiono Tworzenie interfejsu API sieci Web, który wykonuje operacje tworzenia, odczytu, aktualizacji i usuwania (CRUD) w bazie danych [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL.</span><span class="sxs-lookup"><span data-stu-id="6ad73-105">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="6ad73-106">Ten samouczek zawiera informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="6ad73-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6ad73-107">Konfigurowanie MongoDB</span><span class="sxs-lookup"><span data-stu-id="6ad73-107">Configure MongoDB</span></span>
> * <span data-ttu-id="6ad73-108">Tworzenie bazy danych MongoDB</span><span class="sxs-lookup"><span data-stu-id="6ad73-108">Create a MongoDB database</span></span>
> * <span data-ttu-id="6ad73-109">Zdefiniuj kolekcję MongoDB i schemat</span><span class="sxs-lookup"><span data-stu-id="6ad73-109">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="6ad73-110">Wykonywanie operacji MongoDB CRUD z internetowego interfejsu API</span><span class="sxs-lookup"><span data-stu-id="6ad73-110">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="6ad73-111">Dostosowywanie serializacji JSON</span><span class="sxs-lookup"><span data-stu-id="6ad73-111">Customize JSON serialization</span></span>

<span data-ttu-id="6ad73-112">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6ad73-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6ad73-113">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="6ad73-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6ad73-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6ad73-114">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="6ad73-115">Zestaw .NET Core SDK 3.0 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="6ad73-115">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="6ad73-116">[Program Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) z **ASP.NET i programowaniem aplikacji sieci Web**</span><span class="sxs-lookup"><span data-stu-id="6ad73-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="6ad73-117">MongoDB</span><span class="sxs-lookup"><span data-stu-id="6ad73-117">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="6ad73-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6ad73-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="6ad73-119">Zestaw .NET Core SDK 3.0 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="6ad73-119">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="6ad73-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6ad73-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="6ad73-121">Rozszerzenie C# for Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6ad73-121">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="6ad73-122">MongoDB</span><span class="sxs-lookup"><span data-stu-id="6ad73-122">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6ad73-123">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="6ad73-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="6ad73-124">Zestaw .NET Core SDK 3.0 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="6ad73-124">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="6ad73-125">Visual Studio dla komputerów Mac wersja 7,7 lub nowsza</span><span class="sxs-lookup"><span data-stu-id="6ad73-125">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="6ad73-126">MongoDB</span><span class="sxs-lookup"><span data-stu-id="6ad73-126">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="6ad73-127">Konfigurowanie MongoDB</span><span class="sxs-lookup"><span data-stu-id="6ad73-127">Configure MongoDB</span></span>

<span data-ttu-id="6ad73-128">W przypadku korzystania z systemu Windows MongoDB jest instalowany w *C\\:\\Program Files MongoDB* domyślnie.</span><span class="sxs-lookup"><span data-stu-id="6ad73-128">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="6ad73-129">Dodaj *plik C\\: program\\Files\\MongoDB\\\<Server version_number \\>bin* do `Path` zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="6ad73-129">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="6ad73-130">Ta zmiana umożliwia MongoDB dostęp z dowolnego miejsca na komputerze deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="6ad73-130">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="6ad73-131">Użyj powłoki Mongo w poniższych krokach, aby utworzyć bazę danych, utworzyć kolekcje i przechowywać dokumenty.</span><span class="sxs-lookup"><span data-stu-id="6ad73-131">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="6ad73-132">Aby uzyskać więcej informacji na temat poleceń powłoki Mongo, zobacz [Praca z powłoką Mongo](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="6ad73-132">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="6ad73-133">Wybierz katalog na komputerze deweloperskim, który ma być używany do przechowywania danych.</span><span class="sxs-lookup"><span data-stu-id="6ad73-133">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="6ad73-134">Na przykład *C:\\BooksData* w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="6ad73-134">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="6ad73-135">Utwórz katalog, jeśli nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="6ad73-135">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="6ad73-136">Powłoka Mongo nie tworzy nowych katalogów.</span><span class="sxs-lookup"><span data-stu-id="6ad73-136">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="6ad73-137">Otwórz powłokę poleceń.</span><span class="sxs-lookup"><span data-stu-id="6ad73-137">Open a command shell.</span></span> <span data-ttu-id="6ad73-138">Uruchom następujące polecenie, aby nawiązać połączenie z usługą MongoDB na domyślnym porcie 27017.</span><span class="sxs-lookup"><span data-stu-id="6ad73-138">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="6ad73-139">Pamiętaj, aby `<data_directory_path>` zamienić na katalog wybrany w poprzednim kroku.</span><span class="sxs-lookup"><span data-stu-id="6ad73-139">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="6ad73-140">Otwórz inne wystąpienie powłoki poleceń.</span><span class="sxs-lookup"><span data-stu-id="6ad73-140">Open another command shell instance.</span></span> <span data-ttu-id="6ad73-141">Połącz się z domyślną bazą danych testów, uruchamiając następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="6ad73-141">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="6ad73-142">Uruchom następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="6ad73-142">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="6ad73-143">Jeśli jeszcze nie istnieje, zostanie utworzona baza danych o nazwie *BookstoreDb* .</span><span class="sxs-lookup"><span data-stu-id="6ad73-143">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="6ad73-144">Jeśli baza danych istnieje, jego połączenie jest otwierane dla transakcji.</span><span class="sxs-lookup"><span data-stu-id="6ad73-144">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="6ad73-145">Utwórz `Books` kolekcję przy użyciu następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="6ad73-145">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="6ad73-146">Zostanie wyświetlony następujący wynik:</span><span class="sxs-lookup"><span data-stu-id="6ad73-146">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="6ad73-147">Zdefiniuj schemat `Books` kolekcji i Wstaw dwa dokumenty przy użyciu następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="6ad73-147">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="6ad73-148">Zostanie wyświetlony następujący wynik:</span><span class="sxs-lookup"><span data-stu-id="6ad73-148">The following result is displayed:</span></span>

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
   > <span data-ttu-id="6ad73-149">Identyfikator przedstawiony w tym artykule nie będzie pasował do identyfikatorów podczas uruchamiania tego przykładu.</span><span class="sxs-lookup"><span data-stu-id="6ad73-149">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="6ad73-150">Wyświetl dokumenty w bazie danych przy użyciu następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="6ad73-150">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="6ad73-151">Zostanie wyświetlony następujący wynik:</span><span class="sxs-lookup"><span data-stu-id="6ad73-151">The following result is displayed:</span></span>

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

   <span data-ttu-id="6ad73-152">Schemat dodaje automatycznie wygenerowaną `_id` właściwość typu `ObjectId` dla każdego dokumentu.</span><span class="sxs-lookup"><span data-stu-id="6ad73-152">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="6ad73-153">Baza danych jest gotowa.</span><span class="sxs-lookup"><span data-stu-id="6ad73-153">The database is ready.</span></span> <span data-ttu-id="6ad73-154">Możesz rozpocząć tworzenie ASP.NET Core internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="6ad73-154">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="6ad73-155">Tworzenie projektu interfejsu API sieci Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6ad73-155">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6ad73-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6ad73-156">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="6ad73-157">Przejdź do pozycji **plik** > **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="6ad73-157">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="6ad73-158">Wybierz typ projektu **aplikacja sieci Web ASP.NET Core** a następnie wybierz przycisk **dalej**.</span><span class="sxs-lookup"><span data-stu-id="6ad73-158">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="6ad73-159">Nazwij projekt *BooksApi*, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="6ad73-159">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="6ad73-160">Wybierz platformę docelową **.NET Core** i **ASP.NET Core 3,0**.</span><span class="sxs-lookup"><span data-stu-id="6ad73-160">Select the **.NET Core** target framework and **ASP.NET Core 3.0**.</span></span> <span data-ttu-id="6ad73-161">Wybierz szablon projektu **interfejsu API** i wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="6ad73-161">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="6ad73-162">Odwiedź [galerię NuGet: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) , aby określić najnowszą stabilną wersję sterownika .NET dla usługi MongoDB.</span><span class="sxs-lookup"><span data-stu-id="6ad73-162">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="6ad73-163">W oknie **konsola Menedżera pakietów** przejdź do katalogu głównego projektu.</span><span class="sxs-lookup"><span data-stu-id="6ad73-163">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="6ad73-164">Uruchom następujące polecenie, aby zainstalować sterownik .NET dla MongoDB:</span><span class="sxs-lookup"><span data-stu-id="6ad73-164">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="6ad73-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6ad73-165">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="6ad73-166">Uruchom następujące polecenia w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="6ad73-166">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="6ad73-167">Nowy projekt interfejsu API sieci Web ASP.NET Core przeznaczony dla platformy .NET Core został wygenerowany i otwarty w Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="6ad73-167">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="6ad73-168">Gdy ikona płomienia OmniSharp na pasku stanu zmieni kolor na zielony, w oknie dialogowym zostanie wyświetlony monit **o podanie wymaganych zasobów do skompilowania i debugowania z elementu "BooksApi". Dodać je?**.</span><span class="sxs-lookup"><span data-stu-id="6ad73-168">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="6ad73-169">Wybierz pozycję **tak**.</span><span class="sxs-lookup"><span data-stu-id="6ad73-169">Select **Yes**.</span></span>
1. <span data-ttu-id="6ad73-170">Odwiedź [galerię NuGet: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) , aby określić najnowszą stabilną wersję sterownika .NET dla usługi MongoDB.</span><span class="sxs-lookup"><span data-stu-id="6ad73-170">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="6ad73-171">Otwórz **zintegrowany terminal** i przejdź do katalogu głównego projektu.</span><span class="sxs-lookup"><span data-stu-id="6ad73-171">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="6ad73-172">Uruchom następujące polecenie, aby zainstalować sterownik .NET dla MongoDB:</span><span class="sxs-lookup"><span data-stu-id="6ad73-172">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6ad73-173">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="6ad73-173">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="6ad73-174">Przejdź do pozycji **plik** > **nowe rozwiązanie** > **.NET Core** > **.**</span><span class="sxs-lookup"><span data-stu-id="6ad73-174">Go to **File** > **New Solution** > **.NET Core** > **App**.</span></span>
1. <span data-ttu-id="6ad73-175">Wybierz szablon projektu C# **interfejsu API sieci Web ASP.NET Core** i kliknij przycisk **dalej**.</span><span class="sxs-lookup"><span data-stu-id="6ad73-175">Select the **ASP.NET Core Web API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="6ad73-176">Z listy rozwijanej **platforma docelowa** wybierz pozycję **.NET Core 3,0** , a następnie wybierz pozycję **Next (dalej**).</span><span class="sxs-lookup"><span data-stu-id="6ad73-176">Select **.NET Core 3.0** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="6ad73-177">Wprowadź *BooksApi* jako **nazwę projektu**, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="6ad73-177">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="6ad73-178">W konsoli **rozwiązania** kliknij prawym przyciskiem myszy węzeł **zależności** projektu i wybierz polecenie **Dodaj pakiety**.</span><span class="sxs-lookup"><span data-stu-id="6ad73-178">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="6ad73-179">Wprowadź *MongoDB. Driver* w polu wyszukiwania, wybierz pakiet *MongoDB. Driver* , a następnie wybierz pozycję **Dodaj pakiet**.</span><span class="sxs-lookup"><span data-stu-id="6ad73-179">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="6ad73-180">Wybierz przycisk **Akceptuj** w oknie dialogowym **akceptacji licencji** .</span><span class="sxs-lookup"><span data-stu-id="6ad73-180">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="6ad73-181">Dodaj model jednostki</span><span class="sxs-lookup"><span data-stu-id="6ad73-181">Add an entity model</span></span>

1. <span data-ttu-id="6ad73-182">Dodaj katalog *models* do katalogu głównego projektu.</span><span class="sxs-lookup"><span data-stu-id="6ad73-182">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="6ad73-183">Dodaj `Book` klasę do katalogu *modeli* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="6ad73-183">Add a `Book` class to the *Models* directory with the following code:</span></span>

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

   <span data-ttu-id="6ad73-184">W poprzedniej klasie `Id` Właściwość:</span><span class="sxs-lookup"><span data-stu-id="6ad73-184">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="6ad73-185">Jest wymagany do mapowania obiektu środowiska uruchomieniowego języka wspólnego (CLR) do kolekcji MongoDB.</span><span class="sxs-lookup"><span data-stu-id="6ad73-185">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="6ad73-186">Ma adnotację z [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) , aby wyznaczyć tę właściwość jako klucz podstawowy dokumentu.</span><span class="sxs-lookup"><span data-stu-id="6ad73-186">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="6ad73-187">Ma adnotację z [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) , aby zezwolić na przekazywanie parametru jako `string` typ zamiast struktury [objectid](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) .</span><span class="sxs-lookup"><span data-stu-id="6ad73-187">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="6ad73-188">Mongo obsługuje konwersję z `string` do `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="6ad73-188">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="6ad73-189">`BookName` Właściwość ma adnotację z [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) atrybutem.</span><span class="sxs-lookup"><span data-stu-id="6ad73-189">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="6ad73-190">Wartość atrybutu `Name` reprezentuje nazwę właściwości w kolekcji MongoDB.</span><span class="sxs-lookup"><span data-stu-id="6ad73-190">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="6ad73-191">Dodaj model konfiguracji</span><span class="sxs-lookup"><span data-stu-id="6ad73-191">Add a configuration model</span></span>

1. <span data-ttu-id="6ad73-192">Dodaj następujące wartości konfiguracji bazy danych do pliku *appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="6ad73-192">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/3.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="6ad73-193">Dodaj plik *BookstoreDatabaseSettings.cs* do katalogu *models* o następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="6ad73-193">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="6ad73-194">Powyższa `BookstoreDatabaseSettings` Klasa jest używana do przechowywania wartości `BookstoreDatabaseSettings` właściwości *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="6ad73-194">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="6ad73-195">Nazwy właściwości JSON i C# są nazywane identycznie, aby uprościć proces mapowania.</span><span class="sxs-lookup"><span data-stu-id="6ad73-195">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="6ad73-196">Dodaj następujący wyróżniony kod do `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="6ad73-196">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-8)]

   <span data-ttu-id="6ad73-197">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="6ad73-197">In the preceding code:</span></span>

   * <span data-ttu-id="6ad73-198">Wystąpienie konfiguracji, do którego są powiązane `BookstoreDatabaseSettings` sekcje pliku *appSettings. JSON* , jest zarejestrowane w kontenerze iniekcji zależności (di).</span><span class="sxs-lookup"><span data-stu-id="6ad73-198">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="6ad73-199">`BookstoreDatabaseSettings` Na przykład `ConnectionString` właściwość obiektu jest wypełniana `BookstoreDatabaseSettings:ConnectionString` właściwością w pliku *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="6ad73-199">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="6ad73-200">`IBookstoreDatabaseSettings` Interfejs jest rejestrowany przy użyciu programu di z pojedynczym [okresem istnienia usługi](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="6ad73-200">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="6ad73-201">Po dowstrzykiwaniu wystąpienie interfejsu jest rozpoznawane jako `BookstoreDatabaseSettings` obiekt.</span><span class="sxs-lookup"><span data-stu-id="6ad73-201">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="6ad73-202">Dodaj następujący kod na początku *Startup.cs* , aby rozwiązać odwołania `BookstoreDatabaseSettings` i: `IBookstoreDatabaseSettings`</span><span class="sxs-lookup"><span data-stu-id="6ad73-202">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="6ad73-203">Dodawanie usługi operacji CRUD</span><span class="sxs-lookup"><span data-stu-id="6ad73-203">Add a CRUD operations service</span></span>

1. <span data-ttu-id="6ad73-204">Dodaj katalog *usług* do katalogu głównego projektu.</span><span class="sxs-lookup"><span data-stu-id="6ad73-204">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="6ad73-205">Dodaj `BookService` klasę do katalogu *usług* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="6ad73-205">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="6ad73-206">W poprzednim kodzie `IBookstoreDatabaseSettings` wystąpienie jest pobierane z funkcji di przez iniekcję konstruktora.</span><span class="sxs-lookup"><span data-stu-id="6ad73-206">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="6ad73-207">Ta technika zapewnia dostęp do wartości konfiguracyjnych *appSettings. JSON* , które zostały dodane w sekcji [Dodawanie modelu konfiguracji](#add-a-configuration-model) .</span><span class="sxs-lookup"><span data-stu-id="6ad73-207">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="6ad73-208">Dodaj następujący wyróżniony kod do `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="6ad73-208">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="6ad73-209">W poprzednim kodzie `BookService` Klasa jest zarejestrowana przy użyciu funkcji di, aby obsługiwać iniekcję konstruktora w klasach zużywających.</span><span class="sxs-lookup"><span data-stu-id="6ad73-209">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="6ad73-210">Okres istnienia usługi pojedynczej jest najbardziej `BookService` odpowiedni, ponieważ pobiera bezpośrednią zależność od `MongoClient`.</span><span class="sxs-lookup"><span data-stu-id="6ad73-210">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="6ad73-211">Zgodnie z oficjalnymi [wskazówkami dotyczącymi ponownego użycia klienta Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use) `MongoClient` należy zarejestrować się w programie di z pojedynczym okresem istnienia usługi.</span><span class="sxs-lookup"><span data-stu-id="6ad73-211">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="6ad73-212">Dodaj następujący kod na początku *Startup.cs* , aby rozwiązać `BookService` odwołanie:</span><span class="sxs-lookup"><span data-stu-id="6ad73-212">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="6ad73-213">`BookService` Klasa używa następujących `MongoDB.Driver` elementów członkowskich do wykonywania operacji CRUD w bazie danych:</span><span class="sxs-lookup"><span data-stu-id="6ad73-213">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="6ad73-214">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; odczytuje wystąpienie serwera na potrzeby wykonywania operacji bazy danych.</span><span class="sxs-lookup"><span data-stu-id="6ad73-214">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Reads the server instance for performing database operations.</span></span> <span data-ttu-id="6ad73-215">W konstruktorze tej klasy podano parametry połączenia MongoDB:</span><span class="sxs-lookup"><span data-stu-id="6ad73-215">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="6ad73-216">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; reprezentuje bazę danych Mongo do wykonywania operacji.</span><span class="sxs-lookup"><span data-stu-id="6ad73-216">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="6ad73-217">W tym samouczku do uzyskiwania dostępu do danych w określonej kolekcji jest stosowana ogólna Metoda [getcollection\<TDocument> (Collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) .</span><span class="sxs-lookup"><span data-stu-id="6ad73-217">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="6ad73-218">Wykonaj operacje CRUD w odniesieniu do kolekcji po wywołaniu tej metody.</span><span class="sxs-lookup"><span data-stu-id="6ad73-218">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="6ad73-219">W wywołaniu `GetCollection<TDocument>(collection)` metody:</span><span class="sxs-lookup"><span data-stu-id="6ad73-219">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="6ad73-220">`collection`reprezentuje nazwę kolekcji.</span><span class="sxs-lookup"><span data-stu-id="6ad73-220">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="6ad73-221">`TDocument`reprezentuje typ obiektu CLR przechowywany w kolekcji.</span><span class="sxs-lookup"><span data-stu-id="6ad73-221">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="6ad73-222">`GetCollection<TDocument>(collection)`zwraca obiekt [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) reprezentujący kolekcję.</span><span class="sxs-lookup"><span data-stu-id="6ad73-222">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="6ad73-223">W tym samouczku następujące metody są wywoływane w kolekcji:</span><span class="sxs-lookup"><span data-stu-id="6ad73-223">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="6ad73-224">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; usuwa pojedynczy dokument pasujący do podanych kryteriów wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="6ad73-224">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="6ad73-225">[Znajdź\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; zwraca wszystkie dokumenty w kolekcji pasujące do podanych kryteriów wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="6ad73-225">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="6ad73-226">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Wstawia podany obiekt jako nowy dokument w kolekcji.</span><span class="sxs-lookup"><span data-stu-id="6ad73-226">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="6ad73-227">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; zastępuje pojedynczy dokument pasujący do podanych kryteriów wyszukiwania z podanym obiektem.</span><span class="sxs-lookup"><span data-stu-id="6ad73-227">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="6ad73-228">Dodawanie kontrolera</span><span class="sxs-lookup"><span data-stu-id="6ad73-228">Add a controller</span></span>

<span data-ttu-id="6ad73-229">Dodaj `BooksController` klasę do katalogu *controllers* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="6ad73-229">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="6ad73-230">Poprzedni kontroler interfejsu API sieci Web:</span><span class="sxs-lookup"><span data-stu-id="6ad73-230">The preceding web API controller:</span></span>

* <span data-ttu-id="6ad73-231">Używa `BookService` klasy do wykonywania operacji CRUD.</span><span class="sxs-lookup"><span data-stu-id="6ad73-231">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="6ad73-232">Zawiera metody akcji do obsługi żądań HTTP GET, POST, PUT i DELETE.</span><span class="sxs-lookup"><span data-stu-id="6ad73-232">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="6ad73-233">Wywołuje <xref:System.Web.Http.ApiController.CreatedAtRoute*> metodę akcji `Create` , aby zwrócić odpowiedź [http 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) .</span><span class="sxs-lookup"><span data-stu-id="6ad73-233">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="6ad73-234">Kod stanu 201 jest standardową odpowiedzią dla metody POST protokołu HTTP, która tworzy nowy zasób na serwerze.</span><span class="sxs-lookup"><span data-stu-id="6ad73-234">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="6ad73-235">`CreatedAtRoute`dodaje również `Location` nagłówek do odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="6ad73-235">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="6ad73-236">`Location` Nagłówek określa identyfikator URI nowo utworzonej książki.</span><span class="sxs-lookup"><span data-stu-id="6ad73-236">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="6ad73-237">Testowanie interfejsu API sieci Web</span><span class="sxs-lookup"><span data-stu-id="6ad73-237">Test the web API</span></span>

1. <span data-ttu-id="6ad73-238">Skompiluj i uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="6ad73-238">Build and run the app.</span></span>

1. <span data-ttu-id="6ad73-239">Przejdź do `http://localhost:<port>/api/books` w celu przetestowania metody `Get` akcji bez parametrów kontrolera.</span><span class="sxs-lookup"><span data-stu-id="6ad73-239">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="6ad73-240">Zostanie wyświetlona następująca odpowiedź JSON:</span><span class="sxs-lookup"><span data-stu-id="6ad73-240">The following JSON response is displayed:</span></span>

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

1. <span data-ttu-id="6ad73-241">Przejdź do `http://localhost:<port>/api/books/{id here}` , aby przetestować przeciążoną `Get` metodę działania kontrolera.</span><span class="sxs-lookup"><span data-stu-id="6ad73-241">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="6ad73-242">Zostanie wyświetlona następująca odpowiedź JSON:</span><span class="sxs-lookup"><span data-stu-id="6ad73-242">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="6ad73-243">Konfigurowanie opcji serializacji JSON</span><span class="sxs-lookup"><span data-stu-id="6ad73-243">Configure JSON serialization options</span></span>

<span data-ttu-id="6ad73-244">Istnieją dwa szczegóły dotyczące odpowiedzi JSON zwróconych w sekcji [testowanie interfejsu API sieci Web](#test-the-web-api) :</span><span class="sxs-lookup"><span data-stu-id="6ad73-244">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="6ad73-245">Nazwy właściwości "default notacji CamelCase wielkość liter należy zmienić tak, aby pasowały do wielkości liter w języku Pascal nazw właściwości obiektu CLR.</span><span class="sxs-lookup"><span data-stu-id="6ad73-245">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="6ad73-246">`bookName` Właściwość powinna zostać zwrócona jako `Name`.</span><span class="sxs-lookup"><span data-stu-id="6ad73-246">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="6ad73-247">Aby spełnić powyższe wymagania, należy wprowadzić następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="6ad73-247">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="6ad73-248">JSON.NET został usunięty z ASP.NET udostępnionej platformy.</span><span class="sxs-lookup"><span data-stu-id="6ad73-248">JSON.NET has been removed from ASP.NET shared framework.</span></span> <span data-ttu-id="6ad73-249">Dodaj odwołanie do pakietu do [Microsoft. AspNetCore. MVC. NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span><span class="sxs-lookup"><span data-stu-id="6ad73-249">Add a package reference to [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span></span>

1. <span data-ttu-id="6ad73-250">W `Startup.ConfigureServices`programie łańcuchować następujący wyróżniony kod w wywołaniu `AddControllers` metody:</span><span class="sxs-lookup"><span data-stu-id="6ad73-250">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddControllers` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="6ad73-251">W przypadku poprzedniej zmiany nazwy właściwości w serializowanej odpowiedzi JSON interfejsu API sieci Web pasują do odpowiednich nazw właściwości w typie obiektu CLR.</span><span class="sxs-lookup"><span data-stu-id="6ad73-251">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="6ad73-252">Na przykład, serializacja `Book` `Author` właściwości klasy jako `Author`.</span><span class="sxs-lookup"><span data-stu-id="6ad73-252">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="6ad73-253">W *modelach/książka. cs*Dodaj adnotację do `BookName` właściwości z następującym [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) atrybutem:</span><span class="sxs-lookup"><span data-stu-id="6ad73-253">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="6ad73-254">Wartość `[JsonProperty]` atrybutu `Name` reprezentuje nazwę właściwości w SERIALIZOWANEJ odpowiedzi JSON interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="6ad73-254">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="6ad73-255">Dodaj następujący kod na górze *modeli/książek. cs* , aby rozwiązać odwołanie do `[JsonProperty]` atrybutu:</span><span class="sxs-lookup"><span data-stu-id="6ad73-255">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="6ad73-256">Powtórz kroki zdefiniowane w sekcji [testowanie interfejsu API sieci Web](#test-the-web-api) .</span><span class="sxs-lookup"><span data-stu-id="6ad73-256">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="6ad73-257">Zwróć uwagę na różnice w nazwach właściwości JSON.</span><span class="sxs-lookup"><span data-stu-id="6ad73-257">Notice the difference in JSON property names.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6ad73-258">W tym samouczku przedstawiono Tworzenie interfejsu API sieci Web, który wykonuje operacje tworzenia, odczytu, aktualizacji i usuwania (CRUD) w bazie danych [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL.</span><span class="sxs-lookup"><span data-stu-id="6ad73-258">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="6ad73-259">Ten samouczek zawiera informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="6ad73-259">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6ad73-260">Konfigurowanie MongoDB</span><span class="sxs-lookup"><span data-stu-id="6ad73-260">Configure MongoDB</span></span>
> * <span data-ttu-id="6ad73-261">Tworzenie bazy danych MongoDB</span><span class="sxs-lookup"><span data-stu-id="6ad73-261">Create a MongoDB database</span></span>
> * <span data-ttu-id="6ad73-262">Zdefiniuj kolekcję MongoDB i schemat</span><span class="sxs-lookup"><span data-stu-id="6ad73-262">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="6ad73-263">Wykonywanie operacji MongoDB CRUD z internetowego interfejsu API</span><span class="sxs-lookup"><span data-stu-id="6ad73-263">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="6ad73-264">Dostosowywanie serializacji JSON</span><span class="sxs-lookup"><span data-stu-id="6ad73-264">Customize JSON serialization</span></span>

<span data-ttu-id="6ad73-265">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6ad73-265">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6ad73-266">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="6ad73-266">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6ad73-267">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6ad73-267">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="6ad73-268">Zestaw .NET Core SDK 2,2</span><span class="sxs-lookup"><span data-stu-id="6ad73-268">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="6ad73-269">[Program Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) z **ASP.NET i programowaniem aplikacji sieci Web**</span><span class="sxs-lookup"><span data-stu-id="6ad73-269">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="6ad73-270">MongoDB</span><span class="sxs-lookup"><span data-stu-id="6ad73-270">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="6ad73-271">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6ad73-271">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="6ad73-272">Zestaw .NET Core SDK 2,2</span><span class="sxs-lookup"><span data-stu-id="6ad73-272">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="6ad73-273">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6ad73-273">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="6ad73-274">Rozszerzenie C# for Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6ad73-274">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="6ad73-275">MongoDB</span><span class="sxs-lookup"><span data-stu-id="6ad73-275">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6ad73-276">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="6ad73-276">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="6ad73-277">Zestaw .NET Core SDK 2,2</span><span class="sxs-lookup"><span data-stu-id="6ad73-277">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="6ad73-278">Visual Studio dla komputerów Mac wersja 7,7 lub nowsza</span><span class="sxs-lookup"><span data-stu-id="6ad73-278">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="6ad73-279">MongoDB</span><span class="sxs-lookup"><span data-stu-id="6ad73-279">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="6ad73-280">Konfigurowanie MongoDB</span><span class="sxs-lookup"><span data-stu-id="6ad73-280">Configure MongoDB</span></span>

<span data-ttu-id="6ad73-281">W przypadku korzystania z systemu Windows MongoDB jest instalowany w *C\\:\\Program Files MongoDB* domyślnie.</span><span class="sxs-lookup"><span data-stu-id="6ad73-281">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="6ad73-282">Dodaj *plik C\\: program\\Files\\MongoDB\\\<Server version_number \\>bin* do `Path` zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="6ad73-282">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="6ad73-283">Ta zmiana umożliwia MongoDB dostęp z dowolnego miejsca na komputerze deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="6ad73-283">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="6ad73-284">Użyj powłoki Mongo w poniższych krokach, aby utworzyć bazę danych, utworzyć kolekcje i przechowywać dokumenty.</span><span class="sxs-lookup"><span data-stu-id="6ad73-284">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="6ad73-285">Aby uzyskać więcej informacji na temat poleceń powłoki Mongo, zobacz [Praca z powłoką Mongo](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="6ad73-285">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="6ad73-286">Wybierz katalog na komputerze deweloperskim, który ma być używany do przechowywania danych.</span><span class="sxs-lookup"><span data-stu-id="6ad73-286">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="6ad73-287">Na przykład *C:\\BooksData* w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="6ad73-287">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="6ad73-288">Utwórz katalog, jeśli nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="6ad73-288">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="6ad73-289">Powłoka Mongo nie tworzy nowych katalogów.</span><span class="sxs-lookup"><span data-stu-id="6ad73-289">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="6ad73-290">Otwórz powłokę poleceń.</span><span class="sxs-lookup"><span data-stu-id="6ad73-290">Open a command shell.</span></span> <span data-ttu-id="6ad73-291">Uruchom następujące polecenie, aby nawiązać połączenie z usługą MongoDB na domyślnym porcie 27017.</span><span class="sxs-lookup"><span data-stu-id="6ad73-291">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="6ad73-292">Pamiętaj, aby `<data_directory_path>` zamienić na katalog wybrany w poprzednim kroku.</span><span class="sxs-lookup"><span data-stu-id="6ad73-292">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="6ad73-293">Otwórz inne wystąpienie powłoki poleceń.</span><span class="sxs-lookup"><span data-stu-id="6ad73-293">Open another command shell instance.</span></span> <span data-ttu-id="6ad73-294">Połącz się z domyślną bazą danych testów, uruchamiając następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="6ad73-294">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="6ad73-295">Uruchom następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="6ad73-295">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="6ad73-296">Jeśli jeszcze nie istnieje, zostanie utworzona baza danych o nazwie *BookstoreDb* .</span><span class="sxs-lookup"><span data-stu-id="6ad73-296">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="6ad73-297">Jeśli baza danych istnieje, jego połączenie jest otwierane dla transakcji.</span><span class="sxs-lookup"><span data-stu-id="6ad73-297">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="6ad73-298">Utwórz `Books` kolekcję przy użyciu następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="6ad73-298">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="6ad73-299">Zostanie wyświetlony następujący wynik:</span><span class="sxs-lookup"><span data-stu-id="6ad73-299">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="6ad73-300">Zdefiniuj schemat `Books` kolekcji i Wstaw dwa dokumenty przy użyciu następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="6ad73-300">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="6ad73-301">Zostanie wyświetlony następujący wynik:</span><span class="sxs-lookup"><span data-stu-id="6ad73-301">The following result is displayed:</span></span>

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
   > <span data-ttu-id="6ad73-302">Identyfikator przedstawiony w tym artykule nie będzie pasował do identyfikatorów podczas uruchamiania tego przykładu.</span><span class="sxs-lookup"><span data-stu-id="6ad73-302">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="6ad73-303">Wyświetl dokumenty w bazie danych przy użyciu następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="6ad73-303">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="6ad73-304">Zostanie wyświetlony następujący wynik:</span><span class="sxs-lookup"><span data-stu-id="6ad73-304">The following result is displayed:</span></span>

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

   <span data-ttu-id="6ad73-305">Schemat dodaje automatycznie wygenerowaną `_id` właściwość typu `ObjectId` dla każdego dokumentu.</span><span class="sxs-lookup"><span data-stu-id="6ad73-305">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="6ad73-306">Baza danych jest gotowa.</span><span class="sxs-lookup"><span data-stu-id="6ad73-306">The database is ready.</span></span> <span data-ttu-id="6ad73-307">Możesz rozpocząć tworzenie ASP.NET Core internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="6ad73-307">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="6ad73-308">Tworzenie projektu interfejsu API sieci Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6ad73-308">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6ad73-309">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6ad73-309">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="6ad73-310">Przejdź do pozycji **plik** > **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="6ad73-310">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="6ad73-311">Wybierz typ projektu **aplikacja sieci Web ASP.NET Core** a następnie wybierz przycisk **dalej**.</span><span class="sxs-lookup"><span data-stu-id="6ad73-311">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="6ad73-312">Nazwij projekt *BooksApi*, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="6ad73-312">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="6ad73-313">Wybierz platformę docelową **.NET Core** i **ASP.NET Core 2,2**.</span><span class="sxs-lookup"><span data-stu-id="6ad73-313">Select the **.NET Core** target framework and **ASP.NET Core 2.2**.</span></span> <span data-ttu-id="6ad73-314">Wybierz szablon projektu **interfejsu API** i wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="6ad73-314">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="6ad73-315">Odwiedź [galerię NuGet: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) , aby określić najnowszą stabilną wersję sterownika .NET dla usługi MongoDB.</span><span class="sxs-lookup"><span data-stu-id="6ad73-315">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="6ad73-316">W oknie **konsola Menedżera pakietów** przejdź do katalogu głównego projektu.</span><span class="sxs-lookup"><span data-stu-id="6ad73-316">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="6ad73-317">Uruchom następujące polecenie, aby zainstalować sterownik .NET dla MongoDB:</span><span class="sxs-lookup"><span data-stu-id="6ad73-317">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="6ad73-318">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6ad73-318">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="6ad73-319">Uruchom następujące polecenia w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="6ad73-319">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="6ad73-320">Nowy projekt interfejsu API sieci Web ASP.NET Core przeznaczony dla platformy .NET Core został wygenerowany i otwarty w Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="6ad73-320">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="6ad73-321">Gdy ikona płomienia OmniSharp na pasku stanu zmieni kolor na zielony, w oknie dialogowym zostanie wyświetlony monit **o podanie wymaganych zasobów do skompilowania i debugowania z elementu "BooksApi". Dodać je?**.</span><span class="sxs-lookup"><span data-stu-id="6ad73-321">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="6ad73-322">Wybierz pozycję **tak**.</span><span class="sxs-lookup"><span data-stu-id="6ad73-322">Select **Yes**.</span></span>
1. <span data-ttu-id="6ad73-323">Odwiedź [galerię NuGet: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) , aby określić najnowszą stabilną wersję sterownika .NET dla usługi MongoDB.</span><span class="sxs-lookup"><span data-stu-id="6ad73-323">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="6ad73-324">Otwórz **zintegrowany terminal** i przejdź do katalogu głównego projektu.</span><span class="sxs-lookup"><span data-stu-id="6ad73-324">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="6ad73-325">Uruchom następujące polecenie, aby zainstalować sterownik .NET dla MongoDB:</span><span class="sxs-lookup"><span data-stu-id="6ad73-325">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6ad73-326">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="6ad73-326">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="6ad73-327">Przejdź do pozycji **plik** > **nowe rozwiązanie** > **.NET Core** > **.**</span><span class="sxs-lookup"><span data-stu-id="6ad73-327">Go to **File** > **New Solution** > **.NET Core** > **App**.</span></span>
1. <span data-ttu-id="6ad73-328">Wybierz szablon projektu C# **interfejsu API sieci Web ASP.NET Core** i kliknij przycisk **dalej**.</span><span class="sxs-lookup"><span data-stu-id="6ad73-328">Select the **ASP.NET Core Web API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="6ad73-329">Z listy rozwijanej **platforma docelowa** wybierz pozycję **.NET Core 2,2** , a następnie wybierz pozycję **Next (dalej**).</span><span class="sxs-lookup"><span data-stu-id="6ad73-329">Select **.NET Core 2.2** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="6ad73-330">Wprowadź *BooksApi* jako **nazwę projektu**, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="6ad73-330">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="6ad73-331">W konsoli **rozwiązania** kliknij prawym przyciskiem myszy węzeł **zależności** projektu i wybierz polecenie **Dodaj pakiety**.</span><span class="sxs-lookup"><span data-stu-id="6ad73-331">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="6ad73-332">Wprowadź *MongoDB. Driver* w polu wyszukiwania, wybierz pakiet *MongoDB. Driver* , a następnie wybierz pozycję **Dodaj pakiet**.</span><span class="sxs-lookup"><span data-stu-id="6ad73-332">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="6ad73-333">Wybierz przycisk **Akceptuj** w oknie dialogowym **akceptacji licencji** .</span><span class="sxs-lookup"><span data-stu-id="6ad73-333">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="6ad73-334">Dodaj model jednostki</span><span class="sxs-lookup"><span data-stu-id="6ad73-334">Add an entity model</span></span>

1. <span data-ttu-id="6ad73-335">Dodaj katalog *models* do katalogu głównego projektu.</span><span class="sxs-lookup"><span data-stu-id="6ad73-335">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="6ad73-336">Dodaj `Book` klasę do katalogu *modeli* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="6ad73-336">Add a `Book` class to the *Models* directory with the following code:</span></span>

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

   <span data-ttu-id="6ad73-337">W poprzedniej klasie `Id` Właściwość:</span><span class="sxs-lookup"><span data-stu-id="6ad73-337">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="6ad73-338">Jest wymagany do mapowania obiektu środowiska uruchomieniowego języka wspólnego (CLR) do kolekcji MongoDB.</span><span class="sxs-lookup"><span data-stu-id="6ad73-338">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="6ad73-339">Ma adnotację z [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) , aby wyznaczyć tę właściwość jako klucz podstawowy dokumentu.</span><span class="sxs-lookup"><span data-stu-id="6ad73-339">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="6ad73-340">Ma adnotację z [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) , aby zezwolić na przekazywanie parametru jako `string` typ zamiast struktury [objectid](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) .</span><span class="sxs-lookup"><span data-stu-id="6ad73-340">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="6ad73-341">Mongo obsługuje konwersję z `string` do `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="6ad73-341">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="6ad73-342">`BookName` Właściwość ma adnotację z [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) atrybutem.</span><span class="sxs-lookup"><span data-stu-id="6ad73-342">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="6ad73-343">Wartość atrybutu `Name` reprezentuje nazwę właściwości w kolekcji MongoDB.</span><span class="sxs-lookup"><span data-stu-id="6ad73-343">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="6ad73-344">Dodaj model konfiguracji</span><span class="sxs-lookup"><span data-stu-id="6ad73-344">Add a configuration model</span></span>

1. <span data-ttu-id="6ad73-345">Dodaj następujące wartości konfiguracji bazy danych do pliku *appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="6ad73-345">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/2.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="6ad73-346">Dodaj plik *BookstoreDatabaseSettings.cs* do katalogu *models* o następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="6ad73-346">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="6ad73-347">Powyższa `BookstoreDatabaseSettings` Klasa jest używana do przechowywania wartości `BookstoreDatabaseSettings` właściwości *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="6ad73-347">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="6ad73-348">Nazwy właściwości JSON i C# są nazywane identycznie, aby uprościć proces mapowania.</span><span class="sxs-lookup"><span data-stu-id="6ad73-348">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="6ad73-349">Dodaj następujący wyróżniony kod do `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="6ad73-349">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   <span data-ttu-id="6ad73-350">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="6ad73-350">In the preceding code:</span></span>

   * <span data-ttu-id="6ad73-351">Wystąpienie konfiguracji, do którego są powiązane `BookstoreDatabaseSettings` sekcje pliku *appSettings. JSON* , jest zarejestrowane w kontenerze iniekcji zależności (di).</span><span class="sxs-lookup"><span data-stu-id="6ad73-351">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="6ad73-352">`BookstoreDatabaseSettings` Na przykład `ConnectionString` właściwość obiektu jest wypełniana `BookstoreDatabaseSettings:ConnectionString` właściwością w pliku *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="6ad73-352">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="6ad73-353">`IBookstoreDatabaseSettings` Interfejs jest rejestrowany przy użyciu programu di z pojedynczym [okresem istnienia usługi](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="6ad73-353">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="6ad73-354">Po dowstrzykiwaniu wystąpienie interfejsu jest rozpoznawane jako `BookstoreDatabaseSettings` obiekt.</span><span class="sxs-lookup"><span data-stu-id="6ad73-354">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="6ad73-355">Dodaj następujący kod na początku *Startup.cs* , aby rozwiązać odwołania `BookstoreDatabaseSettings` i: `IBookstoreDatabaseSettings`</span><span class="sxs-lookup"><span data-stu-id="6ad73-355">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="6ad73-356">Dodawanie usługi operacji CRUD</span><span class="sxs-lookup"><span data-stu-id="6ad73-356">Add a CRUD operations service</span></span>

1. <span data-ttu-id="6ad73-357">Dodaj katalog *usług* do katalogu głównego projektu.</span><span class="sxs-lookup"><span data-stu-id="6ad73-357">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="6ad73-358">Dodaj `BookService` klasę do katalogu *usług* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="6ad73-358">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="6ad73-359">W poprzednim kodzie `IBookstoreDatabaseSettings` wystąpienie jest pobierane z funkcji di przez iniekcję konstruktora.</span><span class="sxs-lookup"><span data-stu-id="6ad73-359">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="6ad73-360">Ta technika zapewnia dostęp do wartości konfiguracyjnych *appSettings. JSON* , które zostały dodane w sekcji [Dodawanie modelu konfiguracji](#add-a-configuration-model) .</span><span class="sxs-lookup"><span data-stu-id="6ad73-360">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="6ad73-361">Dodaj następujący wyróżniony kod do `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="6ad73-361">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="6ad73-362">W poprzednim kodzie `BookService` Klasa jest zarejestrowana przy użyciu funkcji di, aby obsługiwać iniekcję konstruktora w klasach zużywających.</span><span class="sxs-lookup"><span data-stu-id="6ad73-362">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="6ad73-363">Okres istnienia usługi pojedynczej jest najbardziej `BookService` odpowiedni, ponieważ pobiera bezpośrednią zależność od `MongoClient`.</span><span class="sxs-lookup"><span data-stu-id="6ad73-363">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="6ad73-364">Zgodnie z oficjalnymi [wskazówkami dotyczącymi ponownego użycia klienta Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use) `MongoClient` należy zarejestrować się w programie di z pojedynczym okresem istnienia usługi.</span><span class="sxs-lookup"><span data-stu-id="6ad73-364">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="6ad73-365">Dodaj następujący kod na początku *Startup.cs* , aby rozwiązać `BookService` odwołanie:</span><span class="sxs-lookup"><span data-stu-id="6ad73-365">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="6ad73-366">`BookService` Klasa używa następujących `MongoDB.Driver` elementów członkowskich do wykonywania operacji CRUD w bazie danych:</span><span class="sxs-lookup"><span data-stu-id="6ad73-366">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="6ad73-367">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; odczytuje wystąpienie serwera na potrzeby wykonywania operacji bazy danych.</span><span class="sxs-lookup"><span data-stu-id="6ad73-367">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Reads the server instance for performing database operations.</span></span> <span data-ttu-id="6ad73-368">W konstruktorze tej klasy podano parametry połączenia MongoDB:</span><span class="sxs-lookup"><span data-stu-id="6ad73-368">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="6ad73-369">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; reprezentuje bazę danych Mongo do wykonywania operacji.</span><span class="sxs-lookup"><span data-stu-id="6ad73-369">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="6ad73-370">W tym samouczku do uzyskiwania dostępu do danych w określonej kolekcji jest stosowana ogólna Metoda [getcollection\<TDocument> (Collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) .</span><span class="sxs-lookup"><span data-stu-id="6ad73-370">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="6ad73-371">Wykonaj operacje CRUD w odniesieniu do kolekcji po wywołaniu tej metody.</span><span class="sxs-lookup"><span data-stu-id="6ad73-371">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="6ad73-372">W wywołaniu `GetCollection<TDocument>(collection)` metody:</span><span class="sxs-lookup"><span data-stu-id="6ad73-372">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="6ad73-373">`collection`reprezentuje nazwę kolekcji.</span><span class="sxs-lookup"><span data-stu-id="6ad73-373">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="6ad73-374">`TDocument`reprezentuje typ obiektu CLR przechowywany w kolekcji.</span><span class="sxs-lookup"><span data-stu-id="6ad73-374">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="6ad73-375">`GetCollection<TDocument>(collection)`zwraca obiekt [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) reprezentujący kolekcję.</span><span class="sxs-lookup"><span data-stu-id="6ad73-375">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="6ad73-376">W tym samouczku następujące metody są wywoływane w kolekcji:</span><span class="sxs-lookup"><span data-stu-id="6ad73-376">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="6ad73-377">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; usuwa pojedynczy dokument pasujący do podanych kryteriów wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="6ad73-377">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="6ad73-378">[Znajdź\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; zwraca wszystkie dokumenty w kolekcji pasujące do podanych kryteriów wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="6ad73-378">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="6ad73-379">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Wstawia podany obiekt jako nowy dokument w kolekcji.</span><span class="sxs-lookup"><span data-stu-id="6ad73-379">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="6ad73-380">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; zastępuje pojedynczy dokument pasujący do podanych kryteriów wyszukiwania z podanym obiektem.</span><span class="sxs-lookup"><span data-stu-id="6ad73-380">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="6ad73-381">Dodawanie kontrolera</span><span class="sxs-lookup"><span data-stu-id="6ad73-381">Add a controller</span></span>

<span data-ttu-id="6ad73-382">Dodaj `BooksController` klasę do katalogu *controllers* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="6ad73-382">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="6ad73-383">Poprzedni kontroler interfejsu API sieci Web:</span><span class="sxs-lookup"><span data-stu-id="6ad73-383">The preceding web API controller:</span></span>

* <span data-ttu-id="6ad73-384">Używa `BookService` klasy do wykonywania operacji CRUD.</span><span class="sxs-lookup"><span data-stu-id="6ad73-384">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="6ad73-385">Zawiera metody akcji do obsługi żądań HTTP GET, POST, PUT i DELETE.</span><span class="sxs-lookup"><span data-stu-id="6ad73-385">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="6ad73-386">Wywołuje <xref:System.Web.Http.ApiController.CreatedAtRoute*> metodę akcji `Create` , aby zwrócić odpowiedź [http 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) .</span><span class="sxs-lookup"><span data-stu-id="6ad73-386">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="6ad73-387">Kod stanu 201 jest standardową odpowiedzią dla metody POST protokołu HTTP, która tworzy nowy zasób na serwerze.</span><span class="sxs-lookup"><span data-stu-id="6ad73-387">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="6ad73-388">`CreatedAtRoute`dodaje również `Location` nagłówek do odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="6ad73-388">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="6ad73-389">`Location` Nagłówek określa identyfikator URI nowo utworzonej książki.</span><span class="sxs-lookup"><span data-stu-id="6ad73-389">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="6ad73-390">Testowanie interfejsu API sieci Web</span><span class="sxs-lookup"><span data-stu-id="6ad73-390">Test the web API</span></span>

1. <span data-ttu-id="6ad73-391">Skompiluj i uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="6ad73-391">Build and run the app.</span></span>

1. <span data-ttu-id="6ad73-392">Przejdź do `http://localhost:<port>/api/books` w celu przetestowania metody `Get` akcji bez parametrów kontrolera.</span><span class="sxs-lookup"><span data-stu-id="6ad73-392">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="6ad73-393">Zostanie wyświetlona następująca odpowiedź JSON:</span><span class="sxs-lookup"><span data-stu-id="6ad73-393">The following JSON response is displayed:</span></span>

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

1. <span data-ttu-id="6ad73-394">Przejdź do `http://localhost:<port>/api/books/{id here}` , aby przetestować przeciążoną `Get` metodę działania kontrolera.</span><span class="sxs-lookup"><span data-stu-id="6ad73-394">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="6ad73-395">Zostanie wyświetlona następująca odpowiedź JSON:</span><span class="sxs-lookup"><span data-stu-id="6ad73-395">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="6ad73-396">Konfigurowanie opcji serializacji JSON</span><span class="sxs-lookup"><span data-stu-id="6ad73-396">Configure JSON serialization options</span></span>

<span data-ttu-id="6ad73-397">Istnieją dwa szczegóły dotyczące odpowiedzi JSON zwróconych w sekcji [testowanie interfejsu API sieci Web](#test-the-web-api) :</span><span class="sxs-lookup"><span data-stu-id="6ad73-397">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="6ad73-398">Nazwy właściwości "default notacji CamelCase wielkość liter należy zmienić tak, aby pasowały do wielkości liter w języku Pascal nazw właściwości obiektu CLR.</span><span class="sxs-lookup"><span data-stu-id="6ad73-398">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="6ad73-399">`bookName` Właściwość powinna zostać zwrócona jako `Name`.</span><span class="sxs-lookup"><span data-stu-id="6ad73-399">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="6ad73-400">Aby spełnić powyższe wymagania, należy wprowadzić następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="6ad73-400">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="6ad73-401">W `Startup.ConfigureServices`programie łańcuchować następujący wyróżniony kod w wywołaniu `AddMvc` metody:</span><span class="sxs-lookup"><span data-stu-id="6ad73-401">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddMvc` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="6ad73-402">W przypadku poprzedniej zmiany nazwy właściwości w serializowanej odpowiedzi JSON interfejsu API sieci Web pasują do odpowiednich nazw właściwości w typie obiektu CLR.</span><span class="sxs-lookup"><span data-stu-id="6ad73-402">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="6ad73-403">Na przykład, serializacja `Book` `Author` właściwości klasy jako `Author`.</span><span class="sxs-lookup"><span data-stu-id="6ad73-403">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="6ad73-404">W *modelach/książka. cs*Dodaj adnotację do `BookName` właściwości z następującym [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) atrybutem:</span><span class="sxs-lookup"><span data-stu-id="6ad73-404">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="6ad73-405">Wartość `[JsonProperty]` atrybutu `Name` reprezentuje nazwę właściwości w SERIALIZOWANEJ odpowiedzi JSON interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="6ad73-405">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="6ad73-406">Dodaj następujący kod na górze *modeli/książek. cs* , aby rozwiązać odwołanie do `[JsonProperty]` atrybutu:</span><span class="sxs-lookup"><span data-stu-id="6ad73-406">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="6ad73-407">Powtórz kroki zdefiniowane w sekcji [testowanie interfejsu API sieci Web](#test-the-web-api) .</span><span class="sxs-lookup"><span data-stu-id="6ad73-407">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="6ad73-408">Zwróć uwagę na różnice w nazwach właściwości JSON.</span><span class="sxs-lookup"><span data-stu-id="6ad73-408">Notice the difference in JSON property names.</span></span>

::: moniker-end

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="6ad73-409">Dodawanie obsługi uwierzytelniania do internetowego interfejsu API</span><span class="sxs-lookup"><span data-stu-id="6ad73-409">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="next-steps"></a><span data-ttu-id="6ad73-410">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="6ad73-410">Next steps</span></span>

<span data-ttu-id="6ad73-411">Aby uzyskać więcej informacji na temat tworzenia ASP.NET Core interfejsów API sieci Web, zobacz następujące zasoby:</span><span class="sxs-lookup"><span data-stu-id="6ad73-411">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="6ad73-412">Wersja tego artykułu usługi YouTube</span><span class="sxs-lookup"><span data-stu-id="6ad73-412">YouTube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
