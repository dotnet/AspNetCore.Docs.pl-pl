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
ms.openlocfilehash: 350df417886fe1ea5fef89dc221c217d596768b3
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "93060745"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="8fd10-103">Tworzenie internetowego interfejsu API za pomocą ASP.NET Core i MongoDB</span><span class="sxs-lookup"><span data-stu-id="8fd10-103">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="8fd10-104">Autorzy [Pratik Khandelwal](https://twitter.com/K2Prk) i [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="8fd10-104">By [Pratik Khandelwal](https://twitter.com/K2Prk) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8fd10-105">W tym samouczku przedstawiono Tworzenie interfejsu API sieci Web, który wykonuje operacje tworzenia, odczytu, aktualizacji i usuwania (CRUD) w bazie danych [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL.</span><span class="sxs-lookup"><span data-stu-id="8fd10-105">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="8fd10-106">Ten samouczek zawiera informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="8fd10-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="8fd10-107">Konfigurowanie MongoDB</span><span class="sxs-lookup"><span data-stu-id="8fd10-107">Configure MongoDB</span></span>
> * <span data-ttu-id="8fd10-108">Tworzenie bazy danych MongoDB</span><span class="sxs-lookup"><span data-stu-id="8fd10-108">Create a MongoDB database</span></span>
> * <span data-ttu-id="8fd10-109">Zdefiniuj kolekcję MongoDB i schemat</span><span class="sxs-lookup"><span data-stu-id="8fd10-109">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="8fd10-110">Wykonywanie operacji MongoDB CRUD z internetowego interfejsu API</span><span class="sxs-lookup"><span data-stu-id="8fd10-110">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="8fd10-111">Dostosowywanie serializacji JSON</span><span class="sxs-lookup"><span data-stu-id="8fd10-111">Customize JSON serialization</span></span>

<span data-ttu-id="8fd10-112">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8fd10-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="8fd10-113">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="8fd10-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8fd10-114">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8fd10-114">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="8fd10-115">Zestaw .NET Core SDK 3.0 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="8fd10-115">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="8fd10-116">[Program Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) z **ASP.NET i programowaniem aplikacji sieci Web**</span><span class="sxs-lookup"><span data-stu-id="8fd10-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="8fd10-117">MongoDB</span><span class="sxs-lookup"><span data-stu-id="8fd10-117">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="8fd10-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8fd10-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="8fd10-119">Zestaw .NET Core SDK 3.0 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="8fd10-119">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="8fd10-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8fd10-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="8fd10-121">Rozszerzenie C# for Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8fd10-121">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="8fd10-122">MongoDB</span><span class="sxs-lookup"><span data-stu-id="8fd10-122">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8fd10-123">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="8fd10-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="8fd10-124">Zestaw .NET Core SDK 3.0 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="8fd10-124">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="8fd10-125">Visual Studio dla komputerów Mac wersja 7,7 lub nowsza</span><span class="sxs-lookup"><span data-stu-id="8fd10-125">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="8fd10-126">MongoDB</span><span class="sxs-lookup"><span data-stu-id="8fd10-126">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="8fd10-127">Konfigurowanie MongoDB</span><span class="sxs-lookup"><span data-stu-id="8fd10-127">Configure MongoDB</span></span>

<span data-ttu-id="8fd10-128">W przypadku korzystania z systemu Windows MongoDB jest instalowany w *C: \\ Program Files \\ MongoDB* domyślnie.</span><span class="sxs-lookup"><span data-stu-id="8fd10-128">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="8fd10-129">Dodaj *plik C: \\ Program Files \\ MongoDB \\ Server \\ \<version_number> \\ bin* do `Path` zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="8fd10-129">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="8fd10-130">Ta zmiana umożliwia MongoDB dostęp z dowolnego miejsca na komputerze deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="8fd10-130">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="8fd10-131">Użyj powłoki Mongo w poniższych krokach, aby utworzyć bazę danych, utworzyć kolekcje i przechowywać dokumenty.</span><span class="sxs-lookup"><span data-stu-id="8fd10-131">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="8fd10-132">Aby uzyskać więcej informacji na temat poleceń powłoki Mongo, zobacz [Praca z powłoką Mongo](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="8fd10-132">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="8fd10-133">Wybierz katalog na komputerze deweloperskim, który ma być używany do przechowywania danych.</span><span class="sxs-lookup"><span data-stu-id="8fd10-133">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="8fd10-134">Na przykład *C: \\ BooksData* w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="8fd10-134">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="8fd10-135">Utwórz katalog, jeśli nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="8fd10-135">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="8fd10-136">Powłoka Mongo nie tworzy nowych katalogów.</span><span class="sxs-lookup"><span data-stu-id="8fd10-136">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="8fd10-137">Otwórz powłokę poleceń.</span><span class="sxs-lookup"><span data-stu-id="8fd10-137">Open a command shell.</span></span> <span data-ttu-id="8fd10-138">Uruchom następujące polecenie, aby nawiązać połączenie z usługą MongoDB na domyślnym porcie 27017.</span><span class="sxs-lookup"><span data-stu-id="8fd10-138">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="8fd10-139">Pamiętaj, aby zamienić na `<data_directory_path>` katalog wybrany w poprzednim kroku.</span><span class="sxs-lookup"><span data-stu-id="8fd10-139">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="8fd10-140">Otwórz inne wystąpienie powłoki poleceń.</span><span class="sxs-lookup"><span data-stu-id="8fd10-140">Open another command shell instance.</span></span> <span data-ttu-id="8fd10-141">Połącz się z domyślną bazą danych testów, uruchamiając następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="8fd10-141">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="8fd10-142">Uruchom następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="8fd10-142">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="8fd10-143">Jeśli jeszcze nie istnieje, zostanie utworzona baza danych o nazwie *BookstoreDb* .</span><span class="sxs-lookup"><span data-stu-id="8fd10-143">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="8fd10-144">Jeśli baza danych istnieje, jego połączenie jest otwierane dla transakcji.</span><span class="sxs-lookup"><span data-stu-id="8fd10-144">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="8fd10-145">Utwórz `Books` kolekcję przy użyciu następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="8fd10-145">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="8fd10-146">Zostanie wyświetlony następujący wynik:</span><span class="sxs-lookup"><span data-stu-id="8fd10-146">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="8fd10-147">Zdefiniuj schemat `Books` kolekcji i Wstaw dwa dokumenty przy użyciu następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="8fd10-147">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="8fd10-148">Zostanie wyświetlony następujący wynik:</span><span class="sxs-lookup"><span data-stu-id="8fd10-148">The following result is displayed:</span></span>

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
   > <span data-ttu-id="8fd10-149">Identyfikator przedstawiony w tym artykule nie będzie pasował do identyfikatorów podczas uruchamiania tego przykładu.</span><span class="sxs-lookup"><span data-stu-id="8fd10-149">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="8fd10-150">Wyświetl dokumenty w bazie danych przy użyciu następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="8fd10-150">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="8fd10-151">Zostanie wyświetlony następujący wynik:</span><span class="sxs-lookup"><span data-stu-id="8fd10-151">The following result is displayed:</span></span>

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

   <span data-ttu-id="8fd10-152">Schemat dodaje automatycznie wygenerowaną `_id` Właściwość typu `ObjectId` dla każdego dokumentu.</span><span class="sxs-lookup"><span data-stu-id="8fd10-152">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="8fd10-153">Baza danych jest gotowa.</span><span class="sxs-lookup"><span data-stu-id="8fd10-153">The database is ready.</span></span> <span data-ttu-id="8fd10-154">Możesz rozpocząć tworzenie ASP.NET Core internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="8fd10-154">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="8fd10-155">Tworzenie projektu interfejsu API sieci Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8fd10-155">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8fd10-156">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8fd10-156">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="8fd10-157">Przejdź do pozycji **plik** > **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="8fd10-157">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="8fd10-158">Wybierz typ projektu **aplikacja sieci Web ASP.NET Core** a następnie wybierz przycisk **dalej**.</span><span class="sxs-lookup"><span data-stu-id="8fd10-158">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="8fd10-159">Nazwij projekt *BooksApi*, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="8fd10-159">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="8fd10-160">Wybierz platformę docelową **.NET Core** i **ASP.NET Core 3,0**.</span><span class="sxs-lookup"><span data-stu-id="8fd10-160">Select the **.NET Core** target framework and **ASP.NET Core 3.0**.</span></span> <span data-ttu-id="8fd10-161">Wybierz szablon projektu **interfejsu API** i wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="8fd10-161">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="8fd10-162">Odwiedź [galerię NuGet: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) , aby określić najnowszą stabilną wersję sterownika .NET dla usługi MongoDB.</span><span class="sxs-lookup"><span data-stu-id="8fd10-162">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="8fd10-163">W oknie **konsola Menedżera pakietów** przejdź do katalogu głównego projektu.</span><span class="sxs-lookup"><span data-stu-id="8fd10-163">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="8fd10-164">Uruchom następujące polecenie, aby zainstalować sterownik .NET dla MongoDB:</span><span class="sxs-lookup"><span data-stu-id="8fd10-164">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="8fd10-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8fd10-165">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="8fd10-166">Uruchom następujące polecenia w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="8fd10-166">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="8fd10-167">Nowy projekt interfejsu API sieci Web ASP.NET Core przeznaczony dla platformy .NET Core został wygenerowany i otwarty w Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="8fd10-167">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="8fd10-168">Gdy ikona płomienia OmniSharp na pasku stanu zmieni kolor na zielony, w oknie dialogowym zostanie wyświetlony monit **o podanie wymaganych zasobów do skompilowania i debugowania z elementu "BooksApi". Dodać je?**.</span><span class="sxs-lookup"><span data-stu-id="8fd10-168">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="8fd10-169">Wybierz pozycję **Tak**.</span><span class="sxs-lookup"><span data-stu-id="8fd10-169">Select **Yes**.</span></span>
1. <span data-ttu-id="8fd10-170">Odwiedź [galerię NuGet: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) , aby określić najnowszą stabilną wersję sterownika .NET dla usługi MongoDB.</span><span class="sxs-lookup"><span data-stu-id="8fd10-170">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="8fd10-171">Otwórz **zintegrowany terminal** i przejdź do katalogu głównego projektu.</span><span class="sxs-lookup"><span data-stu-id="8fd10-171">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="8fd10-172">Uruchom następujące polecenie, aby zainstalować sterownik .NET dla MongoDB:</span><span class="sxs-lookup"><span data-stu-id="8fd10-172">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8fd10-173">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="8fd10-173">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="8fd10-174">W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz pozycję **plik**  >  **nowe rozwiązanie**  >  **.NET Core**  >  **aplikacji** z paska bocznego.</span><span class="sxs-lookup"><span data-stu-id="8fd10-174">In Visual Studio for Mac earlier than version 8.6, select **File** > **New Solution** > **.NET Core** > **App** from the sidebar.</span></span> <span data-ttu-id="8fd10-175">W wersji 8,6 lub nowszej wybierz pozycję **plik**  >  **nowe rozwiązanie**  >  **Sieć Web i Aplikacja konsolowa**  >   na pasku bocznym.</span><span class="sxs-lookup"><span data-stu-id="8fd10-175">In version 8.6 or later, select **File** > **New Solution** > **Web and Console** > **App** from the sidebar.</span></span>
1. <span data-ttu-id="8fd10-176">Wybierz  > szablon projektu C# **interfejsu API** ASP.NET Core, a następnie wybierz przycisk **dalej**.</span><span class="sxs-lookup"><span data-stu-id="8fd10-176">Select the **ASP.NET Core** > **API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="8fd10-177">Z listy rozwijanej **platforma docelowa** wybierz pozycję **.NET Core 3,1** , a następnie wybierz pozycję **Next (dalej**).</span><span class="sxs-lookup"><span data-stu-id="8fd10-177">Select **.NET Core 3.1** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="8fd10-178">Wprowadź *BooksApi* jako **nazwę projektu**, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="8fd10-178">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="8fd10-179">W konsoli **rozwiązania** kliknij prawym przyciskiem myszy węzeł **zależności** projektu i wybierz polecenie **Dodaj pakiety**.</span><span class="sxs-lookup"><span data-stu-id="8fd10-179">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="8fd10-180">Wprowadź *MongoDB. Driver* w polu wyszukiwania, wybierz pakiet *MongoDB. Driver* , a następnie wybierz pozycję **Dodaj pakiet**.</span><span class="sxs-lookup"><span data-stu-id="8fd10-180">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="8fd10-181">Wybierz przycisk **Akceptuj** w oknie dialogowym **akceptacji licencji** .</span><span class="sxs-lookup"><span data-stu-id="8fd10-181">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="8fd10-182">Dodaj model jednostki</span><span class="sxs-lookup"><span data-stu-id="8fd10-182">Add an entity model</span></span>

1. <span data-ttu-id="8fd10-183">Dodaj katalog *models* do katalogu głównego projektu.</span><span class="sxs-lookup"><span data-stu-id="8fd10-183">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="8fd10-184">Dodaj `Book` klasę do katalogu *modeli* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="8fd10-184">Add a `Book` class to the *Models* directory with the following code:</span></span>

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

   <span data-ttu-id="8fd10-185">W poprzedniej klasie `Id` Właściwość:</span><span class="sxs-lookup"><span data-stu-id="8fd10-185">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="8fd10-186">Jest wymagany do mapowania obiektu środowiska uruchomieniowego języka wspólnego (CLR) do kolekcji MongoDB.</span><span class="sxs-lookup"><span data-stu-id="8fd10-186">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="8fd10-187">Ma adnotację z, [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) Aby wyznaczyć tę właściwość jako klucz podstawowy dokumentu.</span><span class="sxs-lookup"><span data-stu-id="8fd10-187">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="8fd10-188">Ma adnotację z [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) , aby zezwolić na przekazywanie parametru jako typ `string` zamiast struktury [objectid](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) .</span><span class="sxs-lookup"><span data-stu-id="8fd10-188">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="8fd10-189">Mongo obsługuje konwersję z `string` do `ObjectId` .</span><span class="sxs-lookup"><span data-stu-id="8fd10-189">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="8fd10-190">`BookName`Właściwość ma adnotację z [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) atrybutem.</span><span class="sxs-lookup"><span data-stu-id="8fd10-190">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="8fd10-191">Wartość atrybutu `Name` reprezentuje nazwę właściwości w kolekcji MongoDB.</span><span class="sxs-lookup"><span data-stu-id="8fd10-191">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="8fd10-192">Dodaj model konfiguracji</span><span class="sxs-lookup"><span data-stu-id="8fd10-192">Add a configuration model</span></span>

1. <span data-ttu-id="8fd10-193">Dodaj następujące wartości konfiguracji bazy danych do *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="8fd10-193">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/3.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="8fd10-194">Dodaj plik *BookstoreDatabaseSettings.cs* do katalogu *models* o następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="8fd10-194">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="8fd10-195">Poprzednia `BookstoreDatabaseSettings` Klasa jest używana do przechowywania *appsettings.json* `BookstoreDatabaseSettings` wartości właściwości pliku.</span><span class="sxs-lookup"><span data-stu-id="8fd10-195">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="8fd10-196">Nazwy właściwości JSON i C# są nazywane identycznie, aby uprościć proces mapowania.</span><span class="sxs-lookup"><span data-stu-id="8fd10-196">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="8fd10-197">Dodaj następujący wyróżniony kod do `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="8fd10-197">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-8)]

   <span data-ttu-id="8fd10-198">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="8fd10-198">In the preceding code:</span></span>

   * <span data-ttu-id="8fd10-199">Wystąpienie konfiguracji, do którego *appsettings.json* `BookstoreDatabaseSettings` jest powiązane sekcja pliku, jest zarejestrowane w kontenerze iniekcji zależności (di).</span><span class="sxs-lookup"><span data-stu-id="8fd10-199">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="8fd10-200">Na przykład `BookstoreDatabaseSettings` `ConnectionString` właściwość obiektu jest wypełniana `BookstoreDatabaseSettings:ConnectionString` właściwością w *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="8fd10-200">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="8fd10-201">`IBookstoreDatabaseSettings`Interfejs jest rejestrowany przy użyciu programu di z pojedynczym [okresem istnienia usługi](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="8fd10-201">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="8fd10-202">Po dowstrzykiwaniu wystąpienie interfejsu jest rozpoznawane jako `BookstoreDatabaseSettings` obiekt.</span><span class="sxs-lookup"><span data-stu-id="8fd10-202">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="8fd10-203">Dodaj następujący kod na początku *Startup.cs* , aby rozwiązać `BookstoreDatabaseSettings` `IBookstoreDatabaseSettings` odwołania i:</span><span class="sxs-lookup"><span data-stu-id="8fd10-203">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="8fd10-204">Dodawanie usługi operacji CRUD</span><span class="sxs-lookup"><span data-stu-id="8fd10-204">Add a CRUD operations service</span></span>

1. <span data-ttu-id="8fd10-205">Dodaj katalog *usług* do katalogu głównego projektu.</span><span class="sxs-lookup"><span data-stu-id="8fd10-205">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="8fd10-206">Dodaj `BookService` klasę do katalogu *usług* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="8fd10-206">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="8fd10-207">W poprzednim kodzie `IBookstoreDatabaseSettings` wystąpienie jest pobierane z funkcji di przez iniekcję konstruktora.</span><span class="sxs-lookup"><span data-stu-id="8fd10-207">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="8fd10-208">Ta technika zapewnia dostęp do *appsettings.json* wartości konfiguracyjnych, które zostały dodane w sekcji [Dodawanie modelu konfiguracji](#add-a-configuration-model) .</span><span class="sxs-lookup"><span data-stu-id="8fd10-208">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="8fd10-209">Dodaj następujący wyróżniony kod do `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="8fd10-209">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="8fd10-210">W poprzednim kodzie `BookService` Klasa jest zarejestrowana przy użyciu funkcji di, aby obsługiwać iniekcję konstruktora w klasach zużywających.</span><span class="sxs-lookup"><span data-stu-id="8fd10-210">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="8fd10-211">Okres istnienia usługi pojedynczej jest najbardziej odpowiedni, ponieważ `BookService` Pobiera bezpośrednią zależność od `MongoClient` .</span><span class="sxs-lookup"><span data-stu-id="8fd10-211">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="8fd10-212">Zgodnie z oficjalnymi [wskazówkami dotyczącymi ponownego użycia klienta Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use) `MongoClient` należy zarejestrować się w programie di z pojedynczym okresem istnienia usługi.</span><span class="sxs-lookup"><span data-stu-id="8fd10-212">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="8fd10-213">Dodaj następujący kod na początku *Startup.cs* , aby rozwiązać `BookService` odwołanie:</span><span class="sxs-lookup"><span data-stu-id="8fd10-213">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="8fd10-214">`BookService`Klasa używa następujących `MongoDB.Driver` elementów członkowskich do wykonywania operacji CRUD w bazie danych:</span><span class="sxs-lookup"><span data-stu-id="8fd10-214">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="8fd10-215">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): odczytuje wystąpienie serwera na potrzeby wykonywania operacji bazy danych.</span><span class="sxs-lookup"><span data-stu-id="8fd10-215">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): Reads the server instance for performing database operations.</span></span> <span data-ttu-id="8fd10-216">W konstruktorze tej klasy podano parametry połączenia MongoDB:</span><span class="sxs-lookup"><span data-stu-id="8fd10-216">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="8fd10-217">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): reprezentuje bazę danych Mongo do wykonywania operacji.</span><span class="sxs-lookup"><span data-stu-id="8fd10-217">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="8fd10-218">W tym samouczku do uzyskiwania dostępu do danych w określonej kolekcji jest stosowana ogólna Metoda [getcollection \<TDocument> (Collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) .</span><span class="sxs-lookup"><span data-stu-id="8fd10-218">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="8fd10-219">Wykonaj operacje CRUD w odniesieniu do kolekcji po wywołaniu tej metody.</span><span class="sxs-lookup"><span data-stu-id="8fd10-219">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="8fd10-220">W `GetCollection<TDocument>(collection)` wywołaniu metody:</span><span class="sxs-lookup"><span data-stu-id="8fd10-220">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="8fd10-221">`collection` reprezentuje nazwę kolekcji.</span><span class="sxs-lookup"><span data-stu-id="8fd10-221">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="8fd10-222">`TDocument` reprezentuje typ obiektu CLR przechowywany w kolekcji.</span><span class="sxs-lookup"><span data-stu-id="8fd10-222">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="8fd10-223">`GetCollection<TDocument>(collection)` zwraca obiekt [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) reprezentujący kolekcję.</span><span class="sxs-lookup"><span data-stu-id="8fd10-223">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="8fd10-224">W tym samouczku następujące metody są wywoływane w kolekcji:</span><span class="sxs-lookup"><span data-stu-id="8fd10-224">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="8fd10-225">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Usuwa pojedynczy dokument pasujący do podanych kryteriów wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="8fd10-225">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="8fd10-226">[Find \<TDocument> ](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): zwraca wszystkie dokumenty w kolekcji pasujące do podanych kryteriów wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="8fd10-226">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="8fd10-227">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Wstawia podany obiekt jako nowy dokument w kolekcji.</span><span class="sxs-lookup"><span data-stu-id="8fd10-227">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="8fd10-228">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): zamienia pojedynczy dokument pasujący do podanych kryteriów wyszukiwania z podanym obiektem.</span><span class="sxs-lookup"><span data-stu-id="8fd10-228">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="8fd10-229">Dodawanie kontrolera</span><span class="sxs-lookup"><span data-stu-id="8fd10-229">Add a controller</span></span>

<span data-ttu-id="8fd10-230">Dodaj `BooksController` klasę do katalogu *controllers* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="8fd10-230">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="8fd10-231">Poprzedni kontroler interfejsu API sieci Web:</span><span class="sxs-lookup"><span data-stu-id="8fd10-231">The preceding web API controller:</span></span>

* <span data-ttu-id="8fd10-232">Używa `BookService` klasy do wykonywania operacji CRUD.</span><span class="sxs-lookup"><span data-stu-id="8fd10-232">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="8fd10-233">Zawiera metody akcji do obsługi żądań HTTP GET, POST, PUT i DELETE.</span><span class="sxs-lookup"><span data-stu-id="8fd10-233">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="8fd10-234">Wywołuje <xref:System.Web.Http.ApiController.CreatedAtRoute*> `Create` metodę akcji, aby zwrócić odpowiedź [http 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) .</span><span class="sxs-lookup"><span data-stu-id="8fd10-234">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="8fd10-235">Kod stanu 201 jest standardową odpowiedzią dla metody POST protokołu HTTP, która tworzy nowy zasób na serwerze.</span><span class="sxs-lookup"><span data-stu-id="8fd10-235">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="8fd10-236">`CreatedAtRoute` dodaje również `Location` nagłówek do odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="8fd10-236">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="8fd10-237">`Location`Nagłówek określa identyfikator URI nowo utworzonej książki.</span><span class="sxs-lookup"><span data-stu-id="8fd10-237">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="8fd10-238">Testowanie interfejsu API sieci Web</span><span class="sxs-lookup"><span data-stu-id="8fd10-238">Test the web API</span></span>

1. <span data-ttu-id="8fd10-239">Skompiluj i uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="8fd10-239">Build and run the app.</span></span>

1. <span data-ttu-id="8fd10-240">Przejdź do w `http://localhost:<port>/api/books` celu przetestowania metody akcji bez parametrów kontrolera `Get` .</span><span class="sxs-lookup"><span data-stu-id="8fd10-240">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="8fd10-241">Zostanie wyświetlona następująca odpowiedź JSON:</span><span class="sxs-lookup"><span data-stu-id="8fd10-241">The following JSON response is displayed:</span></span>

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

1. <span data-ttu-id="8fd10-242">Przejdź do `http://localhost:<port>/api/books/{id here}` , aby przetestować przeciążoną `Get` metodę działania kontrolera.</span><span class="sxs-lookup"><span data-stu-id="8fd10-242">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="8fd10-243">Zostanie wyświetlona następująca odpowiedź JSON:</span><span class="sxs-lookup"><span data-stu-id="8fd10-243">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="8fd10-244">Konfigurowanie opcji serializacji JSON</span><span class="sxs-lookup"><span data-stu-id="8fd10-244">Configure JSON serialization options</span></span>

<span data-ttu-id="8fd10-245">Istnieją dwa szczegóły dotyczące odpowiedzi JSON zwróconych w sekcji [testowanie interfejsu API sieci Web](#test-the-web-api) :</span><span class="sxs-lookup"><span data-stu-id="8fd10-245">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="8fd10-246">Nazwy właściwości "default notacji CamelCase wielkość liter należy zmienić tak, aby pasowały do wielkości liter w języku Pascal nazw właściwości obiektu CLR.</span><span class="sxs-lookup"><span data-stu-id="8fd10-246">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="8fd10-247">`bookName`Właściwość powinna zostać zwrócona jako `Name` .</span><span class="sxs-lookup"><span data-stu-id="8fd10-247">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="8fd10-248">Aby spełnić powyższe wymagania, należy wprowadzić następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="8fd10-248">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="8fd10-249">JSON.NET został usunięty z ASP.NET udostępnionej platformy.</span><span class="sxs-lookup"><span data-stu-id="8fd10-249">JSON.NET has been removed from ASP.NET shared framework.</span></span> <span data-ttu-id="8fd10-250">Dodaj odwołanie do pakietu do [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson) .</span><span class="sxs-lookup"><span data-stu-id="8fd10-250">Add a package reference to [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span></span>

1. <span data-ttu-id="8fd10-251">W programie `Startup.ConfigureServices` łańcuchować następujący wyróżniony kod w `AddControllers` wywołaniu metody:</span><span class="sxs-lookup"><span data-stu-id="8fd10-251">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddControllers` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="8fd10-252">W przypadku poprzedniej zmiany nazwy właściwości w serializowanej odpowiedzi JSON interfejsu API sieci Web pasują do odpowiednich nazw właściwości w typie obiektu CLR.</span><span class="sxs-lookup"><span data-stu-id="8fd10-252">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="8fd10-253">Na przykład, `Book` `Author` Serializacja właściwości klasy jako `Author` .</span><span class="sxs-lookup"><span data-stu-id="8fd10-253">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="8fd10-254">W *modelach/książka. cs* Dodaj adnotację do `BookName` właściwości z następującym [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) atrybutem:</span><span class="sxs-lookup"><span data-stu-id="8fd10-254">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="8fd10-255">`[JsonProperty]`Wartość atrybutu `Name` reprezentuje nazwę właściwości w serializowanej odpowiedzi JSON interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="8fd10-255">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="8fd10-256">Dodaj następujący kod na górze *modeli/książek. cs* , aby rozwiązać `[JsonProperty]` odwołanie do atrybutu:</span><span class="sxs-lookup"><span data-stu-id="8fd10-256">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="8fd10-257">Powtórz kroki zdefiniowane w sekcji [testowanie interfejsu API sieci Web](#test-the-web-api) .</span><span class="sxs-lookup"><span data-stu-id="8fd10-257">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="8fd10-258">Zwróć uwagę na różnice w nazwach właściwości JSON.</span><span class="sxs-lookup"><span data-stu-id="8fd10-258">Notice the difference in JSON property names.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8fd10-259">W tym samouczku przedstawiono Tworzenie interfejsu API sieci Web, który wykonuje operacje tworzenia, odczytu, aktualizacji i usuwania (CRUD) w bazie danych [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL.</span><span class="sxs-lookup"><span data-stu-id="8fd10-259">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="8fd10-260">Ten samouczek zawiera informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="8fd10-260">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="8fd10-261">Konfigurowanie MongoDB</span><span class="sxs-lookup"><span data-stu-id="8fd10-261">Configure MongoDB</span></span>
> * <span data-ttu-id="8fd10-262">Tworzenie bazy danych MongoDB</span><span class="sxs-lookup"><span data-stu-id="8fd10-262">Create a MongoDB database</span></span>
> * <span data-ttu-id="8fd10-263">Zdefiniuj kolekcję MongoDB i schemat</span><span class="sxs-lookup"><span data-stu-id="8fd10-263">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="8fd10-264">Wykonywanie operacji MongoDB CRUD z internetowego interfejsu API</span><span class="sxs-lookup"><span data-stu-id="8fd10-264">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="8fd10-265">Dostosowywanie serializacji JSON</span><span class="sxs-lookup"><span data-stu-id="8fd10-265">Customize JSON serialization</span></span>

<span data-ttu-id="8fd10-266">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8fd10-266">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="8fd10-267">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="8fd10-267">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8fd10-268">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8fd10-268">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="8fd10-269">Zestaw .NET Core SDK 2,2</span><span class="sxs-lookup"><span data-stu-id="8fd10-269">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="8fd10-270">[Program Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) z **ASP.NET i programowaniem aplikacji sieci Web**</span><span class="sxs-lookup"><span data-stu-id="8fd10-270">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="8fd10-271">MongoDB</span><span class="sxs-lookup"><span data-stu-id="8fd10-271">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="8fd10-272">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8fd10-272">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="8fd10-273">Zestaw .NET Core SDK 2,2</span><span class="sxs-lookup"><span data-stu-id="8fd10-273">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="8fd10-274">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8fd10-274">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="8fd10-275">Rozszerzenie C# for Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8fd10-275">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="8fd10-276">MongoDB</span><span class="sxs-lookup"><span data-stu-id="8fd10-276">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8fd10-277">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="8fd10-277">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="8fd10-278">Zestaw .NET Core SDK 2,2</span><span class="sxs-lookup"><span data-stu-id="8fd10-278">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="8fd10-279">Visual Studio dla komputerów Mac wersja 7,7 lub nowsza</span><span class="sxs-lookup"><span data-stu-id="8fd10-279">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="8fd10-280">MongoDB</span><span class="sxs-lookup"><span data-stu-id="8fd10-280">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="8fd10-281">Konfigurowanie MongoDB</span><span class="sxs-lookup"><span data-stu-id="8fd10-281">Configure MongoDB</span></span>

<span data-ttu-id="8fd10-282">W przypadku korzystania z systemu Windows MongoDB jest instalowany w *C: \\ Program Files \\ MongoDB* domyślnie.</span><span class="sxs-lookup"><span data-stu-id="8fd10-282">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="8fd10-283">Dodaj *plik C: \\ Program Files \\ MongoDB \\ Server \\ \<version_number> \\ bin* do `Path` zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="8fd10-283">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="8fd10-284">Ta zmiana umożliwia MongoDB dostęp z dowolnego miejsca na komputerze deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="8fd10-284">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="8fd10-285">Użyj powłoki Mongo w poniższych krokach, aby utworzyć bazę danych, utworzyć kolekcje i przechowywać dokumenty.</span><span class="sxs-lookup"><span data-stu-id="8fd10-285">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="8fd10-286">Aby uzyskać więcej informacji na temat poleceń powłoki Mongo, zobacz [Praca z powłoką Mongo](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="8fd10-286">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="8fd10-287">Wybierz katalog na komputerze deweloperskim, który ma być używany do przechowywania danych.</span><span class="sxs-lookup"><span data-stu-id="8fd10-287">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="8fd10-288">Na przykład *C: \\ BooksData* w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="8fd10-288">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="8fd10-289">Utwórz katalog, jeśli nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="8fd10-289">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="8fd10-290">Powłoka Mongo nie tworzy nowych katalogów.</span><span class="sxs-lookup"><span data-stu-id="8fd10-290">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="8fd10-291">Otwórz powłokę poleceń.</span><span class="sxs-lookup"><span data-stu-id="8fd10-291">Open a command shell.</span></span> <span data-ttu-id="8fd10-292">Uruchom następujące polecenie, aby nawiązać połączenie z usługą MongoDB na domyślnym porcie 27017.</span><span class="sxs-lookup"><span data-stu-id="8fd10-292">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="8fd10-293">Pamiętaj, aby zamienić na `<data_directory_path>` katalog wybrany w poprzednim kroku.</span><span class="sxs-lookup"><span data-stu-id="8fd10-293">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="8fd10-294">Otwórz inne wystąpienie powłoki poleceń.</span><span class="sxs-lookup"><span data-stu-id="8fd10-294">Open another command shell instance.</span></span> <span data-ttu-id="8fd10-295">Połącz się z domyślną bazą danych testów, uruchamiając następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="8fd10-295">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="8fd10-296">Uruchom następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="8fd10-296">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="8fd10-297">Jeśli jeszcze nie istnieje, zostanie utworzona baza danych o nazwie *BookstoreDb* .</span><span class="sxs-lookup"><span data-stu-id="8fd10-297">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="8fd10-298">Jeśli baza danych istnieje, jego połączenie jest otwierane dla transakcji.</span><span class="sxs-lookup"><span data-stu-id="8fd10-298">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="8fd10-299">Utwórz `Books` kolekcję przy użyciu następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="8fd10-299">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="8fd10-300">Zostanie wyświetlony następujący wynik:</span><span class="sxs-lookup"><span data-stu-id="8fd10-300">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="8fd10-301">Zdefiniuj schemat `Books` kolekcji i Wstaw dwa dokumenty przy użyciu następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="8fd10-301">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="8fd10-302">Zostanie wyświetlony następujący wynik:</span><span class="sxs-lookup"><span data-stu-id="8fd10-302">The following result is displayed:</span></span>

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
   > <span data-ttu-id="8fd10-303">Identyfikator przedstawiony w tym artykule nie będzie pasował do identyfikatorów podczas uruchamiania tego przykładu.</span><span class="sxs-lookup"><span data-stu-id="8fd10-303">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="8fd10-304">Wyświetl dokumenty w bazie danych przy użyciu następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="8fd10-304">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="8fd10-305">Zostanie wyświetlony następujący wynik:</span><span class="sxs-lookup"><span data-stu-id="8fd10-305">The following result is displayed:</span></span>

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

   <span data-ttu-id="8fd10-306">Schemat dodaje automatycznie wygenerowaną `_id` Właściwość typu `ObjectId` dla każdego dokumentu.</span><span class="sxs-lookup"><span data-stu-id="8fd10-306">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="8fd10-307">Baza danych jest gotowa.</span><span class="sxs-lookup"><span data-stu-id="8fd10-307">The database is ready.</span></span> <span data-ttu-id="8fd10-308">Możesz rozpocząć tworzenie ASP.NET Core internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="8fd10-308">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="8fd10-309">Tworzenie projektu interfejsu API sieci Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8fd10-309">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8fd10-310">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8fd10-310">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="8fd10-311">Przejdź do pozycji **plik** > **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="8fd10-311">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="8fd10-312">Wybierz typ projektu **aplikacja sieci Web ASP.NET Core** a następnie wybierz przycisk **dalej**.</span><span class="sxs-lookup"><span data-stu-id="8fd10-312">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="8fd10-313">Nazwij projekt *BooksApi*, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="8fd10-313">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="8fd10-314">Wybierz platformę docelową **.NET Core** i **ASP.NET Core 2,2**.</span><span class="sxs-lookup"><span data-stu-id="8fd10-314">Select the **.NET Core** target framework and **ASP.NET Core 2.2**.</span></span> <span data-ttu-id="8fd10-315">Wybierz szablon projektu **interfejsu API** i wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="8fd10-315">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="8fd10-316">Odwiedź [galerię NuGet: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) , aby określić najnowszą stabilną wersję sterownika .NET dla usługi MongoDB.</span><span class="sxs-lookup"><span data-stu-id="8fd10-316">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="8fd10-317">W oknie **konsola Menedżera pakietów** przejdź do katalogu głównego projektu.</span><span class="sxs-lookup"><span data-stu-id="8fd10-317">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="8fd10-318">Uruchom następujące polecenie, aby zainstalować sterownik .NET dla MongoDB:</span><span class="sxs-lookup"><span data-stu-id="8fd10-318">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="8fd10-319">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8fd10-319">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="8fd10-320">Uruchom następujące polecenia w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="8fd10-320">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="8fd10-321">Nowy projekt interfejsu API sieci Web ASP.NET Core przeznaczony dla platformy .NET Core został wygenerowany i otwarty w Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="8fd10-321">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="8fd10-322">Gdy ikona płomienia OmniSharp na pasku stanu zmieni kolor na zielony, w oknie dialogowym zostanie wyświetlony monit **o podanie wymaganych zasobów do skompilowania i debugowania z elementu "BooksApi". Dodać je?**.</span><span class="sxs-lookup"><span data-stu-id="8fd10-322">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="8fd10-323">Wybierz pozycję **Tak**.</span><span class="sxs-lookup"><span data-stu-id="8fd10-323">Select **Yes**.</span></span>
1. <span data-ttu-id="8fd10-324">Odwiedź [galerię NuGet: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) , aby określić najnowszą stabilną wersję sterownika .NET dla usługi MongoDB.</span><span class="sxs-lookup"><span data-stu-id="8fd10-324">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="8fd10-325">Otwórz **zintegrowany terminal** i przejdź do katalogu głównego projektu.</span><span class="sxs-lookup"><span data-stu-id="8fd10-325">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="8fd10-326">Uruchom następujące polecenie, aby zainstalować sterownik .NET dla MongoDB:</span><span class="sxs-lookup"><span data-stu-id="8fd10-326">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8fd10-327">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="8fd10-327">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="8fd10-328">W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz pozycję **plik**  >  **nowe rozwiązanie**  >  **.NET Core**  >  **aplikacji** z paska bocznego.</span><span class="sxs-lookup"><span data-stu-id="8fd10-328">In Visual Studio for Mac earlier than version 8.6, select **File** > **New Solution** > **.NET Core** > **App** from the sidebar.</span></span> <span data-ttu-id="8fd10-329">W wersji 8,6 lub nowszej wybierz pozycję **plik**  >  **nowe rozwiązanie**  >  **Sieć Web i Aplikacja konsolowa**  >   na pasku bocznym.</span><span class="sxs-lookup"><span data-stu-id="8fd10-329">In version 8.6 or later, select **File** > **New Solution** > **Web and Console** > **App** from the sidebar.</span></span>
1. <span data-ttu-id="8fd10-330">Wybierz szablon projektu C# **interfejsu API sieci Web ASP.NET Core** i kliknij przycisk **dalej**.</span><span class="sxs-lookup"><span data-stu-id="8fd10-330">Select the **ASP.NET Core Web API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="8fd10-331">Z listy rozwijanej **platforma docelowa** wybierz pozycję **.NET Core 2,2** , a następnie wybierz pozycję **Next (dalej**).</span><span class="sxs-lookup"><span data-stu-id="8fd10-331">Select **.NET Core 2.2** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="8fd10-332">Wprowadź *BooksApi* jako **nazwę projektu**, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="8fd10-332">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="8fd10-333">W konsoli **rozwiązania** kliknij prawym przyciskiem myszy węzeł **zależności** projektu i wybierz polecenie **Dodaj pakiety**.</span><span class="sxs-lookup"><span data-stu-id="8fd10-333">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="8fd10-334">Wprowadź *MongoDB. Driver* w polu wyszukiwania, wybierz pakiet *MongoDB. Driver* , a następnie wybierz pozycję **Dodaj pakiet**.</span><span class="sxs-lookup"><span data-stu-id="8fd10-334">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="8fd10-335">Wybierz przycisk **Akceptuj** w oknie dialogowym **akceptacji licencji** .</span><span class="sxs-lookup"><span data-stu-id="8fd10-335">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="8fd10-336">Dodaj model jednostki</span><span class="sxs-lookup"><span data-stu-id="8fd10-336">Add an entity model</span></span>

1. <span data-ttu-id="8fd10-337">Dodaj katalog *models* do katalogu głównego projektu.</span><span class="sxs-lookup"><span data-stu-id="8fd10-337">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="8fd10-338">Dodaj `Book` klasę do katalogu *modeli* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="8fd10-338">Add a `Book` class to the *Models* directory with the following code:</span></span>

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

   <span data-ttu-id="8fd10-339">W poprzedniej klasie `Id` Właściwość:</span><span class="sxs-lookup"><span data-stu-id="8fd10-339">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="8fd10-340">Jest wymagany do mapowania obiektu środowiska uruchomieniowego języka wspólnego (CLR) do kolekcji MongoDB.</span><span class="sxs-lookup"><span data-stu-id="8fd10-340">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="8fd10-341">Ma adnotację z, [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) Aby wyznaczyć tę właściwość jako klucz podstawowy dokumentu.</span><span class="sxs-lookup"><span data-stu-id="8fd10-341">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="8fd10-342">Ma adnotację z [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) , aby zezwolić na przekazywanie parametru jako typ `string` zamiast struktury [objectid](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) .</span><span class="sxs-lookup"><span data-stu-id="8fd10-342">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="8fd10-343">Mongo obsługuje konwersję z `string` do `ObjectId` .</span><span class="sxs-lookup"><span data-stu-id="8fd10-343">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="8fd10-344">`BookName`Właściwość ma adnotację z [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) atrybutem.</span><span class="sxs-lookup"><span data-stu-id="8fd10-344">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="8fd10-345">Wartość atrybutu `Name` reprezentuje nazwę właściwości w kolekcji MongoDB.</span><span class="sxs-lookup"><span data-stu-id="8fd10-345">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="8fd10-346">Dodaj model konfiguracji</span><span class="sxs-lookup"><span data-stu-id="8fd10-346">Add a configuration model</span></span>

1. <span data-ttu-id="8fd10-347">Dodaj następujące wartości konfiguracji bazy danych do *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="8fd10-347">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/2.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="8fd10-348">Dodaj plik *BookstoreDatabaseSettings.cs* do katalogu *models* o następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="8fd10-348">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="8fd10-349">Poprzednia `BookstoreDatabaseSettings` Klasa jest używana do przechowywania *appsettings.json* `BookstoreDatabaseSettings` wartości właściwości pliku.</span><span class="sxs-lookup"><span data-stu-id="8fd10-349">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="8fd10-350">Nazwy właściwości JSON i C# są nazywane identycznie, aby uprościć proces mapowania.</span><span class="sxs-lookup"><span data-stu-id="8fd10-350">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="8fd10-351">Dodaj następujący wyróżniony kod do `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="8fd10-351">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   <span data-ttu-id="8fd10-352">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="8fd10-352">In the preceding code:</span></span>

   * <span data-ttu-id="8fd10-353">Wystąpienie konfiguracji, do którego *appsettings.json* `BookstoreDatabaseSettings` jest powiązane sekcja pliku, jest zarejestrowane w kontenerze iniekcji zależności (di).</span><span class="sxs-lookup"><span data-stu-id="8fd10-353">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="8fd10-354">Na przykład `BookstoreDatabaseSettings` `ConnectionString` właściwość obiektu jest wypełniana `BookstoreDatabaseSettings:ConnectionString` właściwością w *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="8fd10-354">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="8fd10-355">`IBookstoreDatabaseSettings`Interfejs jest rejestrowany przy użyciu programu di z pojedynczym [okresem istnienia usługi](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="8fd10-355">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="8fd10-356">Po dowstrzykiwaniu wystąpienie interfejsu jest rozpoznawane jako `BookstoreDatabaseSettings` obiekt.</span><span class="sxs-lookup"><span data-stu-id="8fd10-356">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="8fd10-357">Dodaj następujący kod na początku *Startup.cs* , aby rozwiązać `BookstoreDatabaseSettings` `IBookstoreDatabaseSettings` odwołania i:</span><span class="sxs-lookup"><span data-stu-id="8fd10-357">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="8fd10-358">Dodawanie usługi operacji CRUD</span><span class="sxs-lookup"><span data-stu-id="8fd10-358">Add a CRUD operations service</span></span>

1. <span data-ttu-id="8fd10-359">Dodaj katalog *usług* do katalogu głównego projektu.</span><span class="sxs-lookup"><span data-stu-id="8fd10-359">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="8fd10-360">Dodaj `BookService` klasę do katalogu *usług* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="8fd10-360">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="8fd10-361">W poprzednim kodzie `IBookstoreDatabaseSettings` wystąpienie jest pobierane z funkcji di przez iniekcję konstruktora.</span><span class="sxs-lookup"><span data-stu-id="8fd10-361">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="8fd10-362">Ta technika zapewnia dostęp do *appsettings.json* wartości konfiguracyjnych, które zostały dodane w sekcji [Dodawanie modelu konfiguracji](#add-a-configuration-model) .</span><span class="sxs-lookup"><span data-stu-id="8fd10-362">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="8fd10-363">Dodaj następujący wyróżniony kod do `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="8fd10-363">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="8fd10-364">W poprzednim kodzie `BookService` Klasa jest zarejestrowana przy użyciu funkcji di, aby obsługiwać iniekcję konstruktora w klasach zużywających.</span><span class="sxs-lookup"><span data-stu-id="8fd10-364">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="8fd10-365">Okres istnienia usługi pojedynczej jest najbardziej odpowiedni, ponieważ `BookService` Pobiera bezpośrednią zależność od `MongoClient` .</span><span class="sxs-lookup"><span data-stu-id="8fd10-365">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="8fd10-366">Zgodnie z oficjalnymi [wskazówkami dotyczącymi ponownego użycia klienta Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use) `MongoClient` należy zarejestrować się w programie di z pojedynczym okresem istnienia usługi.</span><span class="sxs-lookup"><span data-stu-id="8fd10-366">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="8fd10-367">Dodaj następujący kod na początku *Startup.cs* , aby rozwiązać `BookService` odwołanie:</span><span class="sxs-lookup"><span data-stu-id="8fd10-367">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="8fd10-368">`BookService`Klasa używa następujących `MongoDB.Driver` elementów członkowskich do wykonywania operacji CRUD w bazie danych:</span><span class="sxs-lookup"><span data-stu-id="8fd10-368">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="8fd10-369">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): odczytuje wystąpienie serwera na potrzeby wykonywania operacji bazy danych.</span><span class="sxs-lookup"><span data-stu-id="8fd10-369">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): Reads the server instance for performing database operations.</span></span> <span data-ttu-id="8fd10-370">W konstruktorze tej klasy podano parametry połączenia MongoDB:</span><span class="sxs-lookup"><span data-stu-id="8fd10-370">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="8fd10-371">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): reprezentuje bazę danych Mongo do wykonywania operacji.</span><span class="sxs-lookup"><span data-stu-id="8fd10-371">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="8fd10-372">W tym samouczku do uzyskiwania dostępu do danych w określonej kolekcji jest stosowana ogólna Metoda [getcollection \<TDocument> (Collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) .</span><span class="sxs-lookup"><span data-stu-id="8fd10-372">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="8fd10-373">Wykonaj operacje CRUD w odniesieniu do kolekcji po wywołaniu tej metody.</span><span class="sxs-lookup"><span data-stu-id="8fd10-373">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="8fd10-374">W `GetCollection<TDocument>(collection)` wywołaniu metody:</span><span class="sxs-lookup"><span data-stu-id="8fd10-374">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="8fd10-375">`collection` reprezentuje nazwę kolekcji.</span><span class="sxs-lookup"><span data-stu-id="8fd10-375">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="8fd10-376">`TDocument` reprezentuje typ obiektu CLR przechowywany w kolekcji.</span><span class="sxs-lookup"><span data-stu-id="8fd10-376">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="8fd10-377">`GetCollection<TDocument>(collection)` zwraca obiekt [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) reprezentujący kolekcję.</span><span class="sxs-lookup"><span data-stu-id="8fd10-377">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="8fd10-378">W tym samouczku następujące metody są wywoływane w kolekcji:</span><span class="sxs-lookup"><span data-stu-id="8fd10-378">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="8fd10-379">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Usuwa pojedynczy dokument pasujący do podanych kryteriów wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="8fd10-379">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="8fd10-380">[Find \<TDocument> ](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): zwraca wszystkie dokumenty w kolekcji pasujące do podanych kryteriów wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="8fd10-380">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="8fd10-381">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Wstawia podany obiekt jako nowy dokument w kolekcji.</span><span class="sxs-lookup"><span data-stu-id="8fd10-381">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="8fd10-382">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): zamienia pojedynczy dokument pasujący do podanych kryteriów wyszukiwania z podanym obiektem.</span><span class="sxs-lookup"><span data-stu-id="8fd10-382">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="8fd10-383">Dodawanie kontrolera</span><span class="sxs-lookup"><span data-stu-id="8fd10-383">Add a controller</span></span>

<span data-ttu-id="8fd10-384">Dodaj `BooksController` klasę do katalogu *controllers* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="8fd10-384">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="8fd10-385">Poprzedni kontroler interfejsu API sieci Web:</span><span class="sxs-lookup"><span data-stu-id="8fd10-385">The preceding web API controller:</span></span>

* <span data-ttu-id="8fd10-386">Używa `BookService` klasy do wykonywania operacji CRUD.</span><span class="sxs-lookup"><span data-stu-id="8fd10-386">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="8fd10-387">Zawiera metody akcji do obsługi żądań HTTP GET, POST, PUT i DELETE.</span><span class="sxs-lookup"><span data-stu-id="8fd10-387">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="8fd10-388">Wywołuje <xref:System.Web.Http.ApiController.CreatedAtRoute*> `Create` metodę akcji, aby zwrócić odpowiedź [http 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) .</span><span class="sxs-lookup"><span data-stu-id="8fd10-388">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="8fd10-389">Kod stanu 201 jest standardową odpowiedzią dla metody POST protokołu HTTP, która tworzy nowy zasób na serwerze.</span><span class="sxs-lookup"><span data-stu-id="8fd10-389">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="8fd10-390">`CreatedAtRoute` dodaje również `Location` nagłówek do odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="8fd10-390">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="8fd10-391">`Location`Nagłówek określa identyfikator URI nowo utworzonej książki.</span><span class="sxs-lookup"><span data-stu-id="8fd10-391">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="8fd10-392">Testowanie interfejsu API sieci Web</span><span class="sxs-lookup"><span data-stu-id="8fd10-392">Test the web API</span></span>

1. <span data-ttu-id="8fd10-393">Skompiluj i uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="8fd10-393">Build and run the app.</span></span>

1. <span data-ttu-id="8fd10-394">Przejdź do w `http://localhost:<port>/api/books` celu przetestowania metody akcji bez parametrów kontrolera `Get` .</span><span class="sxs-lookup"><span data-stu-id="8fd10-394">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="8fd10-395">Zostanie wyświetlona następująca odpowiedź JSON:</span><span class="sxs-lookup"><span data-stu-id="8fd10-395">The following JSON response is displayed:</span></span>

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

1. <span data-ttu-id="8fd10-396">Przejdź do `http://localhost:<port>/api/books/{id here}` , aby przetestować przeciążoną `Get` metodę działania kontrolera.</span><span class="sxs-lookup"><span data-stu-id="8fd10-396">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="8fd10-397">Zostanie wyświetlona następująca odpowiedź JSON:</span><span class="sxs-lookup"><span data-stu-id="8fd10-397">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="8fd10-398">Konfigurowanie opcji serializacji JSON</span><span class="sxs-lookup"><span data-stu-id="8fd10-398">Configure JSON serialization options</span></span>

<span data-ttu-id="8fd10-399">Istnieją dwa szczegóły dotyczące odpowiedzi JSON zwróconych w sekcji [testowanie interfejsu API sieci Web](#test-the-web-api) :</span><span class="sxs-lookup"><span data-stu-id="8fd10-399">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="8fd10-400">Nazwy właściwości "default notacji CamelCase wielkość liter należy zmienić tak, aby pasowały do wielkości liter w języku Pascal nazw właściwości obiektu CLR.</span><span class="sxs-lookup"><span data-stu-id="8fd10-400">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="8fd10-401">`bookName`Właściwość powinna zostać zwrócona jako `Name` .</span><span class="sxs-lookup"><span data-stu-id="8fd10-401">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="8fd10-402">Aby spełnić powyższe wymagania, należy wprowadzić następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="8fd10-402">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="8fd10-403">W programie `Startup.ConfigureServices` łańcuchować następujący wyróżniony kod w `AddMvc` wywołaniu metody:</span><span class="sxs-lookup"><span data-stu-id="8fd10-403">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddMvc` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="8fd10-404">W przypadku poprzedniej zmiany nazwy właściwości w serializowanej odpowiedzi JSON interfejsu API sieci Web pasują do odpowiednich nazw właściwości w typie obiektu CLR.</span><span class="sxs-lookup"><span data-stu-id="8fd10-404">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="8fd10-405">Na przykład, `Book` `Author` Serializacja właściwości klasy jako `Author` .</span><span class="sxs-lookup"><span data-stu-id="8fd10-405">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="8fd10-406">W *modelach/książka. cs* Dodaj adnotację do `BookName` właściwości z następującym [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) atrybutem:</span><span class="sxs-lookup"><span data-stu-id="8fd10-406">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="8fd10-407">`[JsonProperty]`Wartość atrybutu `Name` reprezentuje nazwę właściwości w serializowanej odpowiedzi JSON interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="8fd10-407">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="8fd10-408">Dodaj następujący kod na górze *modeli/książek. cs* , aby rozwiązać `[JsonProperty]` odwołanie do atrybutu:</span><span class="sxs-lookup"><span data-stu-id="8fd10-408">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="8fd10-409">Powtórz kroki zdefiniowane w sekcji [testowanie interfejsu API sieci Web](#test-the-web-api) .</span><span class="sxs-lookup"><span data-stu-id="8fd10-409">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="8fd10-410">Zwróć uwagę na różnice w nazwach właściwości JSON.</span><span class="sxs-lookup"><span data-stu-id="8fd10-410">Notice the difference in JSON property names.</span></span>

::: moniker-end

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="8fd10-411">Dodawanie obsługi uwierzytelniania do internetowego interfejsu API</span><span class="sxs-lookup"><span data-stu-id="8fd10-411">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="next-steps"></a><span data-ttu-id="8fd10-412">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="8fd10-412">Next steps</span></span>

<span data-ttu-id="8fd10-413">Aby uzyskać więcej informacji na temat tworzenia ASP.NET Core interfejsów API sieci Web, zobacz następujące zasoby:</span><span class="sxs-lookup"><span data-stu-id="8fd10-413">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="8fd10-414">Wersja tego artykułu usługi YouTube</span><span class="sxs-lookup"><span data-stu-id="8fd10-414">YouTube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
