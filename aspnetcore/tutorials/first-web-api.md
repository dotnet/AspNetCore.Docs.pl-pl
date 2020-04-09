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
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="151f5-103">Samouczek: Tworzenie internetowego interfejsu API z ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="151f5-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="151f5-104">Rick [Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5)i [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="151f5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="151f5-105">Ten samouczek uczy podstaw tworzenia internetowego interfejsu API za pomocą ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="151f5-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="151f5-106">Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="151f5-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="151f5-107">Tworzenie projektu internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="151f5-107">Create a web API project.</span></span>
> * <span data-ttu-id="151f5-108">Dodaj klasę modelu i kontekst bazy danych.</span><span class="sxs-lookup"><span data-stu-id="151f5-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="151f5-109">Rusztowanie sterownika z metodami CRUD.</span><span class="sxs-lookup"><span data-stu-id="151f5-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="151f5-110">Konfigurowanie routingu, ścieżek adresów URL i wartości zwracanych.</span><span class="sxs-lookup"><span data-stu-id="151f5-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="151f5-111">Wywołanie internetowego interfejsu API za pomocą listonosza.</span><span class="sxs-lookup"><span data-stu-id="151f5-111">Call the web API with Postman.</span></span>

<span data-ttu-id="151f5-112">Na końcu masz internetowy interfejs API, który może zarządzać elementami "do wykonania" przechowywanymi w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="151f5-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="151f5-113">Omówienie</span><span class="sxs-lookup"><span data-stu-id="151f5-113">Overview</span></span>

<span data-ttu-id="151f5-114">W tym samouczku utworzy się następujący interfejs API:</span><span class="sxs-lookup"><span data-stu-id="151f5-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="151f5-115">interfejs API</span><span class="sxs-lookup"><span data-stu-id="151f5-115">API</span></span> | <span data-ttu-id="151f5-116">Opis</span><span class="sxs-lookup"><span data-stu-id="151f5-116">Description</span></span> | <span data-ttu-id="151f5-117">Treść żądania</span><span class="sxs-lookup"><span data-stu-id="151f5-117">Request body</span></span> | <span data-ttu-id="151f5-118">Treść odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="151f5-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="151f5-119">POBIERZ /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="151f5-119">GET /api/TodoItems</span></span> | <span data-ttu-id="151f5-120">Pobierz wszystkie przedmioty do zrobienia</span><span class="sxs-lookup"><span data-stu-id="151f5-120">Get all to-do items</span></span> | <span data-ttu-id="151f5-121">Brak</span><span class="sxs-lookup"><span data-stu-id="151f5-121">None</span></span> | <span data-ttu-id="151f5-122">Tablica elementów do wykonania</span><span class="sxs-lookup"><span data-stu-id="151f5-122">Array of to-do items</span></span>|
|<span data-ttu-id="151f5-123">POBIERZ /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="151f5-123">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="151f5-124">Uzyskaj przedmiot według identyfikatora</span><span class="sxs-lookup"><span data-stu-id="151f5-124">Get an item by ID</span></span> | <span data-ttu-id="151f5-125">Brak</span><span class="sxs-lookup"><span data-stu-id="151f5-125">None</span></span> | <span data-ttu-id="151f5-126">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="151f5-126">To-do item</span></span>|
|<span data-ttu-id="151f5-127">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="151f5-127">POST /api/TodoItems</span></span> | <span data-ttu-id="151f5-128">Dodawanie nowego elementu</span><span class="sxs-lookup"><span data-stu-id="151f5-128">Add a new item</span></span> | <span data-ttu-id="151f5-129">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="151f5-129">To-do item</span></span> | <span data-ttu-id="151f5-130">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="151f5-130">To-do item</span></span> |
|<span data-ttu-id="151f5-131">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="151f5-131">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="151f5-132">Aktualizowanie istniejącego elementu&nbsp;</span><span class="sxs-lookup"><span data-stu-id="151f5-132">Update an existing item &nbsp;</span></span> | <span data-ttu-id="151f5-133">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="151f5-133">To-do item</span></span> | <span data-ttu-id="151f5-134">Brak</span><span class="sxs-lookup"><span data-stu-id="151f5-134">None</span></span> |
|<span data-ttu-id="151f5-135">USUŃ /api/TodoItems/{id} &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="151f5-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="151f5-136">Usuwanie elementu &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="151f5-136">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="151f5-137">Brak</span><span class="sxs-lookup"><span data-stu-id="151f5-137">None</span></span> | <span data-ttu-id="151f5-138">Brak</span><span class="sxs-lookup"><span data-stu-id="151f5-138">None</span></span>|

<span data-ttu-id="151f5-139">Na poniższym diagramie przedstawiono projekt aplikacji.</span><span class="sxs-lookup"><span data-stu-id="151f5-139">The following diagram shows the design of the app.</span></span>

![Klient jest reprezentowany przez pole po lewej stronie.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="151f5-145">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="151f5-145">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="151f5-146">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="151f5-146">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="151f5-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="151f5-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="151f5-148">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="151f5-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="151f5-149">Tworzenie projektu sieci Web</span><span class="sxs-lookup"><span data-stu-id="151f5-149">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="151f5-150">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="151f5-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="151f5-151">Z menu **Plik** wybierz polecenie **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="151f5-151">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="151f5-152">Wybierz szablon **ASP.NET Core Web Application** i kliknij przycisk **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="151f5-152">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="151f5-153">Nazwij projekt *TodoApi* i kliknij przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="151f5-153">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="151f5-154">W oknie **dialogowym Tworzenie nowej ASP.NET podstawowej aplikacji sieci Web** upewnij się, że wybrano opcję **.NET Core** i ASP.NET Core **3.1.**</span><span class="sxs-lookup"><span data-stu-id="151f5-154">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="151f5-155">Wybierz szablon **interfejsu API** i kliknij przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="151f5-155">Select the **API** template and click **Create**.</span></span>

![Vs nowe okno dialogowe projektu](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="151f5-157">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="151f5-157">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="151f5-158">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="151f5-158">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="151f5-159">Zmień katalogi`cd`( ) na folder, który będzie zawierał folder projektu.</span><span class="sxs-lookup"><span data-stu-id="151f5-159">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="151f5-160">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="151f5-160">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="151f5-161">Gdy w oknie dialogowym pojawi się pytanie, czy chcesz dodać wymagane zasoby do projektu, wybierz opcję **Tak**.</span><span class="sxs-lookup"><span data-stu-id="151f5-161">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="151f5-162">Poprzednie polecenia:</span><span class="sxs-lookup"><span data-stu-id="151f5-162">The preceding commands:</span></span>

  * <span data-ttu-id="151f5-163">Tworzy nowy projekt interfejsu API sieci web i otwiera go w programie Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="151f5-163">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="151f5-164">Dodaje pakiety NuGet, które są wymagane w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="151f5-164">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="151f5-165">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="151f5-165">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="151f5-166">Wybierz **pozycję Plik** > **nowego rozwiązania**.</span><span class="sxs-lookup"><span data-stu-id="151f5-166">Select **File** > **New Solution**.</span></span>

  ![macOS Nowe rozwiązanie](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="151f5-168">Wybierz następnie **pozycję .NET Core** > **App** > **API** > **.**</span><span class="sxs-lookup"><span data-stu-id="151f5-168">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS Nowe okno dialogowe projektu](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="151f5-170">W oknie dialogowym **Konfigurowanie nowego interfejsu API sieci Web ASP.NET Core** wybierz pozycję Target **Framework** of \**.NET Core 3.1*.</span><span class="sxs-lookup"><span data-stu-id="151f5-170">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.1*.</span></span>

* <span data-ttu-id="151f5-171">Wprowadź *pozycję TodoApi* dla **nazwy projektu,** a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="151f5-171">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![okno dialogowe konfiguracji](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="151f5-173">Otwórz terminal poleceń w folderze projektu i uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="151f5-173">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="151f5-174">Testowanie interfejsu API</span><span class="sxs-lookup"><span data-stu-id="151f5-174">Test the API</span></span>

<span data-ttu-id="151f5-175">Szablon projektu tworzy `WeatherForecast` interfejs API.</span><span class="sxs-lookup"><span data-stu-id="151f5-175">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="151f5-176">Wywołanie `Get` metody z przeglądarki, aby przetestować aplikację.</span><span class="sxs-lookup"><span data-stu-id="151f5-176">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="151f5-177">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="151f5-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="151f5-178">Naciśnij klawisze Ctrl+F5, aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="151f5-178">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="151f5-179">Visual Studio uruchamia przeglądarkę `https://localhost:<port>/WeatherForecast`i `<port>` przechodzi do , gdzie jest losowo wybrany numer portu.</span><span class="sxs-lookup"><span data-stu-id="151f5-179">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="151f5-180">Jeśli pojawi się okno dialogowe z pytaniem, czy certyfikat IIS Express ma być zaufany, wybierz opcję **Tak**.</span><span class="sxs-lookup"><span data-stu-id="151f5-180">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="151f5-181">W oknie dialogowym **Ostrzeżenie o zabezpieczeniach,** które pojawi się obok, wybierz pozycję **Tak**.</span><span class="sxs-lookup"><span data-stu-id="151f5-181">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="151f5-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="151f5-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="151f5-183">Naciśnij klawisze Ctrl+F5, aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="151f5-183">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="151f5-184">W przeglądarce przejdź do `https://localhost:5001/WeatherForecast`następującego adresu URL: .</span><span class="sxs-lookup"><span data-stu-id="151f5-184">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="151f5-185">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="151f5-185">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="151f5-186">Wybierz **uruchom** > **debugowanie startowe,** aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="151f5-186">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="151f5-187">Program Visual Studio dla komputerów `https://localhost:<port>`Mac `<port>` uruchamia przeglądarkę i przechodzi do miejsca, w którym jest losowo wybrany numer portu.</span><span class="sxs-lookup"><span data-stu-id="151f5-187">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="151f5-188">Zwracany jest błąd HTTP 404 (nie znaleziono).</span><span class="sxs-lookup"><span data-stu-id="151f5-188">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="151f5-189">Dołącz `/WeatherForecast` do adresu URL (zmień `https://localhost:<port>/WeatherForecast`adres URL na ).</span><span class="sxs-lookup"><span data-stu-id="151f5-189">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="151f5-190">JSON podobne do następujących jest zwracany:</span><span class="sxs-lookup"><span data-stu-id="151f5-190">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="151f5-191">Dodawanie klasy modelu</span><span class="sxs-lookup"><span data-stu-id="151f5-191">Add a model class</span></span>

<span data-ttu-id="151f5-192">*Model* to zestaw klas, które reprezentują dane, którymi zarządza aplikacja.</span><span class="sxs-lookup"><span data-stu-id="151f5-192">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="151f5-193">Model dla tej aplikacji `TodoItem` jest pojedynczą klasą.</span><span class="sxs-lookup"><span data-stu-id="151f5-193">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="151f5-194">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="151f5-194">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="151f5-195">W **Eksploratorze rozwiązań**kliknij projekt prawym przyciskiem myszy.</span><span class="sxs-lookup"><span data-stu-id="151f5-195">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="151f5-196">Wybierz **pozycję Dodaj** > **nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="151f5-196">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="151f5-197">Nazwij folder *Models*.</span><span class="sxs-lookup"><span data-stu-id="151f5-197">Name the folder *Models*.</span></span>

* <span data-ttu-id="151f5-198">Kliknij prawym przyciskiem myszy folder *Modele* i wybierz polecenie **Dodaj** > **klasę**.</span><span class="sxs-lookup"><span data-stu-id="151f5-198">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="151f5-199">Nazwij klasę *TodoItem* i wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="151f5-199">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="151f5-200">Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="151f5-200">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="151f5-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="151f5-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="151f5-202">Dodawanie folderu o nazwie *Modele*.</span><span class="sxs-lookup"><span data-stu-id="151f5-202">Add a folder named *Models*.</span></span>

* <span data-ttu-id="151f5-203">Dodaj `TodoItem` klasę do folderu *Models* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="151f5-203">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="151f5-204">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="151f5-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="151f5-205">Kliknij prawym przyciskiem myszy projekt.</span><span class="sxs-lookup"><span data-stu-id="151f5-205">Right-click the project.</span></span> <span data-ttu-id="151f5-206">Wybierz **pozycję Dodaj** > **nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="151f5-206">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="151f5-207">Nazwij folder *Models*.</span><span class="sxs-lookup"><span data-stu-id="151f5-207">Name the folder *Models*.</span></span>

  ![nowy folder](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="151f5-209">Kliknij prawym przyciskiem myszy folder *Modele* i wybierz polecenie **Dodaj** > **nową** > **klasę opróżnienia ogólnego** > **Empty Class**pliku .</span><span class="sxs-lookup"><span data-stu-id="151f5-209">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="151f5-210">Nazwij klasę *TodoItem*, a następnie kliknij przycisk **Nowy**.</span><span class="sxs-lookup"><span data-stu-id="151f5-210">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="151f5-211">Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="151f5-211">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="151f5-212">Właściwość `Id` działa jako unikatowy klucz w relacyjnej bazie danych.</span><span class="sxs-lookup"><span data-stu-id="151f5-212">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="151f5-213">Klasy modelu można przejść w dowolnym miejscu w projekcie, ale *Models* folder jest używany przez konwencję.</span><span class="sxs-lookup"><span data-stu-id="151f5-213">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="151f5-214">Dodawanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="151f5-214">Add a database context</span></span>

<span data-ttu-id="151f5-215">*Kontekst bazy danych* jest klasą główną, która koordynuje funkcjonalność entity framework dla modelu danych.</span><span class="sxs-lookup"><span data-stu-id="151f5-215">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="151f5-216">Ta klasa jest tworzona `Microsoft.EntityFrameworkCore.DbContext` przez wyprowadzanie z klasy.</span><span class="sxs-lookup"><span data-stu-id="151f5-216">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="151f5-217">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="151f5-217">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="151f5-218">Dodaj serwer Microsoft.EntityFrameCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="151f5-218">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="151f5-219">Z menu **Narzędzia** wybierz polecenie **Menedżer pakietów NuGet > Zarządzanie pakietami NuGet dla rozwiązania**.</span><span class="sxs-lookup"><span data-stu-id="151f5-219">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="151f5-220">Wybierz kartę **Przeglądaj,** a następnie wprowadź w polu wyszukiwania pozycję **Microsoft.EntityFrameCore.SqlServer.**</span><span class="sxs-lookup"><span data-stu-id="151f5-220">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="151f5-221">Wybierz **pozycję Microsoft.EntityFrameWorkCore.SqlServer** w lewym okienku.</span><span class="sxs-lookup"><span data-stu-id="151f5-221">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="151f5-222">Zaznacz pole wyboru **Projekt** w prawym okienku, a następnie wybierz pozycję **Zainstaluj**.</span><span class="sxs-lookup"><span data-stu-id="151f5-222">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="151f5-223">Użyj poprzednich instrukcji, aby `Microsoft.EntityFrameworkCore.InMemory` dodać pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="151f5-223">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![Menedżer pakietów NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="151f5-225">Dodawanie kontekstu bazy danych TodoContext</span><span class="sxs-lookup"><span data-stu-id="151f5-225">Add the TodoContext database context</span></span>

* <span data-ttu-id="151f5-226">Kliknij prawym przyciskiem myszy folder *Modele* i wybierz polecenie **Dodaj** > **klasę**.</span><span class="sxs-lookup"><span data-stu-id="151f5-226">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="151f5-227">Nazwij klasę *TodoContext* i kliknij przycisk **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="151f5-227">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="151f5-228">Kod programu Visual Studio / Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="151f5-228">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="151f5-229">Dodaj `TodoContext` klasę do folderu *Modele.*</span><span class="sxs-lookup"><span data-stu-id="151f5-229">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="151f5-230">Wprowadź następujący kod:</span><span class="sxs-lookup"><span data-stu-id="151f5-230">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="151f5-231">Rejestrowanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="151f5-231">Register the database context</span></span>

<span data-ttu-id="151f5-232">W ASP.NET Core usługi, takie jak kontekst bazy danych, muszą być zarejestrowane w kontenerze [iniekcji zależności (DI).](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="151f5-232">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="151f5-233">Kontener zapewnia usługę do kontrolerów.</span><span class="sxs-lookup"><span data-stu-id="151f5-233">The container provides the service to controllers.</span></span>

<span data-ttu-id="151f5-234">Zaktualizuj *Startup.cs* następującym wyróżnionym kodem:</span><span class="sxs-lookup"><span data-stu-id="151f5-234">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="151f5-235">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="151f5-235">The preceding code:</span></span>

* <span data-ttu-id="151f5-236">Usuwa nieużywane `using` deklaracje.</span><span class="sxs-lookup"><span data-stu-id="151f5-236">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="151f5-237">Dodaje kontekst bazy danych do kontenera DI.</span><span class="sxs-lookup"><span data-stu-id="151f5-237">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="151f5-238">Określa, że kontekst bazy danych będzie używany w bazie danych w pamięci.</span><span class="sxs-lookup"><span data-stu-id="151f5-238">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="151f5-239">Rusztowanie sterownika</span><span class="sxs-lookup"><span data-stu-id="151f5-239">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="151f5-240">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="151f5-240">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="151f5-241">Kliknij prawym przyciskiem myszy folder *Kontrolery.*</span><span class="sxs-lookup"><span data-stu-id="151f5-241">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="151f5-242">Wybierz **pozycję Dodaj** > **nowy element szkieletu**.</span><span class="sxs-lookup"><span data-stu-id="151f5-242">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="151f5-243">Wybierz **kontroler interfejsu API z akcjami, używając entity framework**, a następnie wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="151f5-243">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="151f5-244">W oknie dialogowym **Dodawanie kontrolera interfejsu API z akcjami przy użyciu** programu Entity Framework:</span><span class="sxs-lookup"><span data-stu-id="151f5-244">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="151f5-245">Wybierz **todoItem (TodoApi.Models)** w **klasie Model**.</span><span class="sxs-lookup"><span data-stu-id="151f5-245">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="151f5-246">Wybierz **todoContext (TodoApi.Models)** w **klasie kontekstu danych**.</span><span class="sxs-lookup"><span data-stu-id="151f5-246">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="151f5-247">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="151f5-247">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="151f5-248">Kod programu Visual Studio / Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="151f5-248">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="151f5-249">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="151f5-249">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="151f5-250">Poprzednie polecenia:</span><span class="sxs-lookup"><span data-stu-id="151f5-250">The preceding commands:</span></span>

* <span data-ttu-id="151f5-251">Dodaj pakiety NuGet wymagane dla rusztowania.</span><span class="sxs-lookup"><span data-stu-id="151f5-251">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="151f5-252">Montuje silnik rusztowania`dotnet-aspnet-codegenerator`( ).</span><span class="sxs-lookup"><span data-stu-id="151f5-252">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="151f5-253">Rusztowania `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="151f5-253">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="151f5-254">Wygenerowany kod:</span><span class="sxs-lookup"><span data-stu-id="151f5-254">The generated code:</span></span>

* <span data-ttu-id="151f5-255">Oznacza klasę atrybutem. [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute)</span><span class="sxs-lookup"><span data-stu-id="151f5-255">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="151f5-256">Ten atrybut wskazuje, że kontroler odpowiada na żądania interfejsu API sieci web.</span><span class="sxs-lookup"><span data-stu-id="151f5-256">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="151f5-257">Aby uzyskać informacje o określonych zachowaniach, <xref:web-api/index>które umożliwia atrybut, zobacz .</span><span class="sxs-lookup"><span data-stu-id="151f5-257">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="151f5-258">Używa DI, aby wstrzyknąć kontekst bazy danych (`TodoContext`) do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="151f5-258">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="151f5-259">Kontekst bazy danych jest używany w każdej z metod [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) w kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="151f5-259">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="151f5-260">Szablony ASP.NET Core dla:</span><span class="sxs-lookup"><span data-stu-id="151f5-260">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="151f5-261">Kontrolery z `[action]` widokami zawierają w szablonie trasy.</span><span class="sxs-lookup"><span data-stu-id="151f5-261">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="151f5-262">Kontrolery interfejsu API `[action]` nie zawierają w szablonie trasy.</span><span class="sxs-lookup"><span data-stu-id="151f5-262">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="151f5-263">Gdy `[action]` tokenu nie ma w szablonie trasy, nazwa [akcji](xref:mvc/controllers/routing#action) jest wykluczona z trasy.</span><span class="sxs-lookup"><span data-stu-id="151f5-263">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="151f5-264">Oznacza to, że nazwa skojarzonej metody akcji nie jest używana w pasującej marszrutie.</span><span class="sxs-lookup"><span data-stu-id="151f5-264">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="151f5-265">Sprawdź PostTodoItem create metody</span><span class="sxs-lookup"><span data-stu-id="151f5-265">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="151f5-266">Zastąp return `PostTodoItem` instrukcji w aby użyć [nameof](/dotnet/csharp/language-reference/operators/nameof) operatora:</span><span class="sxs-lookup"><span data-stu-id="151f5-266">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="151f5-267">Poprzedni kod jest http post metody, zgodnie [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) z atrybutem.</span><span class="sxs-lookup"><span data-stu-id="151f5-267">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="151f5-268">Metoda pobiera wartość elementu do wykonania z treści żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="151f5-268">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="151f5-269">Metoda: <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*></span><span class="sxs-lookup"><span data-stu-id="151f5-269">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="151f5-270">Zwraca kod stanu HTTP 201, jeśli zakończy się pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="151f5-270">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="151f5-271">HTTP 201 jest standardową odpowiedzią dla metody HTTP POST, która tworzy nowy zasób na serwerze.</span><span class="sxs-lookup"><span data-stu-id="151f5-271">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="151f5-272">Dodaje nagłówek [lokalizacja](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) do odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="151f5-272">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="151f5-273">Nagłówek `Location` określa [identyfikator URI](https://developer.mozilla.org/docs/Glossary/URI) nowo utworzonego elementu do wykonania.</span><span class="sxs-lookup"><span data-stu-id="151f5-273">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="151f5-274">Aby uzyskać więcej informacji, zobacz [10.2.2 201 Utworzono](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="151f5-274">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="151f5-275">Odwołuje się `GetTodoItem` do akcji, aby utworzyć `Location` identyfikator URI nagłówka.</span><span class="sxs-lookup"><span data-stu-id="151f5-275">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="151f5-276">C# `nameof` słowo kluczowe jest używany w celu uniknięcia `CreatedAtAction` twardego kodowania nazwę akcji w wywołaniu.</span><span class="sxs-lookup"><span data-stu-id="151f5-276">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="151f5-277">Instalowanie listonosza</span><span class="sxs-lookup"><span data-stu-id="151f5-277">Install Postman</span></span>

<span data-ttu-id="151f5-278">Ten samouczek używa postmana do testowania internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="151f5-278">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="151f5-279">Instalowanie [listonosza](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="151f5-279">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="151f5-280">Uruchom aplikację internetową.</span><span class="sxs-lookup"><span data-stu-id="151f5-280">Start the web app.</span></span>
* <span data-ttu-id="151f5-281">Rozpocznij postman.</span><span class="sxs-lookup"><span data-stu-id="151f5-281">Start Postman.</span></span>
* <span data-ttu-id="151f5-282">Wyłączanie **weryfikacji certyfikatu SSL**</span><span class="sxs-lookup"><span data-stu-id="151f5-282">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="151f5-283">W **obszarze** > **Ustawienia** plików (karta**Ogólne)** wyłącz **weryfikację certyfikatu SSL**.</span><span class="sxs-lookup"><span data-stu-id="151f5-283">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="151f5-284">Ponownie włącz weryfikację certyfikatu SSL po przetestowaniu sterownika.</span><span class="sxs-lookup"><span data-stu-id="151f5-284">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="151f5-285">Test PostTodoItem z listonoszem</span><span class="sxs-lookup"><span data-stu-id="151f5-285">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="151f5-286">Utwórz nowe żądanie.</span><span class="sxs-lookup"><span data-stu-id="151f5-286">Create a new request.</span></span>
* <span data-ttu-id="151f5-287">Ustaw metodę HTTP `POST`na .</span><span class="sxs-lookup"><span data-stu-id="151f5-287">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="151f5-288">Wybierz kartę **Treść**.</span><span class="sxs-lookup"><span data-stu-id="151f5-288">Select the **Body** tab.</span></span>
* <span data-ttu-id="151f5-289">Wybierz **nieprzetworzony** przycisk radiowy.</span><span class="sxs-lookup"><span data-stu-id="151f5-289">Select the **raw** radio button.</span></span>
* <span data-ttu-id="151f5-290">Ustaw typ na **JSON (application/json)**.</span><span class="sxs-lookup"><span data-stu-id="151f5-290">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="151f5-291">W treści żądania wprowadź JSON dla elementu do wykonania:</span><span class="sxs-lookup"><span data-stu-id="151f5-291">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="151f5-292">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="151f5-292">Select **Send**.</span></span>

  ![Listonosz z żądaniem utworzenia](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="151f5-294">Testowanie identyfikatora URI nagłówka lokalizacji</span><span class="sxs-lookup"><span data-stu-id="151f5-294">Test the location header URI</span></span>

* <span data-ttu-id="151f5-295">Wybierz kartę **Nagłówki** w okienku **Odpowiedzi.**</span><span class="sxs-lookup"><span data-stu-id="151f5-295">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="151f5-296">Skopiuj wartość **nagłówka Lokalizacja:**</span><span class="sxs-lookup"><span data-stu-id="151f5-296">Copy the **Location** header value:</span></span>

  ![Karta Nagłówki konsoli Listonosz](first-web-api/_static/3/create.png)

* <span data-ttu-id="151f5-298">Ustaw metodę na GET.</span><span class="sxs-lookup"><span data-stu-id="151f5-298">Set the method to GET.</span></span>
* <span data-ttu-id="151f5-299">Wklej identyfikator URI (na przykład `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="151f5-299">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="151f5-300">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="151f5-300">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="151f5-301">Sprawdź metody GET</span><span class="sxs-lookup"><span data-stu-id="151f5-301">Examine the GET methods</span></span>

<span data-ttu-id="151f5-302">Te metody implementują dwa punkty końcowe GET:</span><span class="sxs-lookup"><span data-stu-id="151f5-302">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="151f5-303">Przetestuj aplikację, wywołując dwa punkty końcowe z przeglądarki lub listonosza.</span><span class="sxs-lookup"><span data-stu-id="151f5-303">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="151f5-304">Przykład:</span><span class="sxs-lookup"><span data-stu-id="151f5-304">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="151f5-305">Odpowiedź podobna do następującej jest `GetTodoItems`wywoływana przez wezwanie do:</span><span class="sxs-lookup"><span data-stu-id="151f5-305">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="151f5-306">Test Get z Listonoszem</span><span class="sxs-lookup"><span data-stu-id="151f5-306">Test Get with Postman</span></span>

* <span data-ttu-id="151f5-307">Utwórz nowe żądanie.</span><span class="sxs-lookup"><span data-stu-id="151f5-307">Create a new request.</span></span>
* <span data-ttu-id="151f5-308">Ustaw metodę HTTP na **GET**.</span><span class="sxs-lookup"><span data-stu-id="151f5-308">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="151f5-309">Ustaw adres URL `https://localhost:<port>/api/TodoItems`żądania na .</span><span class="sxs-lookup"><span data-stu-id="151f5-309">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="151f5-310">Na przykład `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="151f5-310">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="151f5-311">Ustaw **widok dwóch okienek** w postmanie.</span><span class="sxs-lookup"><span data-stu-id="151f5-311">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="151f5-312">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="151f5-312">Select **Send**.</span></span>

<span data-ttu-id="151f5-313">Ta aplikacja korzysta z bazy danych w pamięci.</span><span class="sxs-lookup"><span data-stu-id="151f5-313">This app uses an in-memory database.</span></span> <span data-ttu-id="151f5-314">Jeśli aplikacja zostanie zatrzymana i uruchomiona, poprzednie żądanie GET nie zwróci żadnych danych.</span><span class="sxs-lookup"><span data-stu-id="151f5-314">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="151f5-315">Jeśli żadne dane nie są zwracane, dane [POST](#post) do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="151f5-315">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="151f5-316">Ścieżki routingu i adresów URL</span><span class="sxs-lookup"><span data-stu-id="151f5-316">Routing and URL paths</span></span>

<span data-ttu-id="151f5-317">Atrybut [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) oznacza metodę, która odpowiada na żądanie HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="151f5-317">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="151f5-318">Ścieżka adresu URL dla każdej metody jest skonstruowana w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="151f5-318">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="151f5-319">Zacznij od ciągu szablonu w `Route` atrybucie kontrolera:</span><span class="sxs-lookup"><span data-stu-id="151f5-319">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="151f5-320">Zamień `[controller]` na nazwę kontrolera, który zgodnie z konwencją jest nazwą klasy kontrolera minus sufiks "Controller".</span><span class="sxs-lookup"><span data-stu-id="151f5-320">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="151f5-321">W tym przykładzie nazwa klasy kontrolera to **TodoItems**Controller, więc nazwa kontrolera to "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="151f5-321">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="151f5-322">ASP.NET [Routingu rdzenia](xref:mvc/controllers/routing) jest niewrażliwe na wielkości liter.</span><span class="sxs-lookup"><span data-stu-id="151f5-322">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="151f5-323">Jeśli `[HttpGet]` atrybut ma szablon trasy (na `[HttpGet("products")]`przykład ), dołącz go do ścieżki.</span><span class="sxs-lookup"><span data-stu-id="151f5-323">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="151f5-324">W tym przykładzie nie jest używany szablon.</span><span class="sxs-lookup"><span data-stu-id="151f5-324">This sample doesn't use a template.</span></span> <span data-ttu-id="151f5-325">Aby uzyskać więcej informacji, zobacz [Routing atrybutów z atrybutami Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="151f5-325">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="151f5-326">W poniższej `GetTodoItem` `"{id}"` metodzie jest zmienną zastępczą dla unikatowego identyfikatora elementu do wykonania.</span><span class="sxs-lookup"><span data-stu-id="151f5-326">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="151f5-327">Po `GetTodoItem` wywołaniu wartość `"{id}"` w adresie URL jest dostarczana `id` do metody w jego parametrze.</span><span class="sxs-lookup"><span data-stu-id="151f5-327">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="151f5-328">Zwracane wartości</span><span class="sxs-lookup"><span data-stu-id="151f5-328">Return values</span></span>

<span data-ttu-id="151f5-329">Typ zwracany `GetTodoItems` i `GetTodoItem` metody jest [ActionResult\<T> typu](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="151f5-329">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="151f5-330">ASP.NET Core automatycznie serializuje obiekt do [JSON](https://www.json.org/) i zapisuje JSON w treści komunikatu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="151f5-330">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="151f5-331">Kod odpowiedzi dla tego typu zwracania wynosi 200, przy założeniu, że nie ma żadnych nieobsługiwał wyjątków.</span><span class="sxs-lookup"><span data-stu-id="151f5-331">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="151f5-332">Nieobsługiwały się wyjątkami są tłumaczone na błędy 5xx.</span><span class="sxs-lookup"><span data-stu-id="151f5-332">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="151f5-333">`ActionResult`zwracane typy mogą reprezentować szeroki zakres kodów stanu HTTP.</span><span class="sxs-lookup"><span data-stu-id="151f5-333">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="151f5-334">Na przykład `GetTodoItem` może zwrócić dwie różne wartości stanu:</span><span class="sxs-lookup"><span data-stu-id="151f5-334">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="151f5-335">Jeśli żaden element nie pasuje do żądanego identyfikatora, metoda zwraca kod błędu [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) 404.</span><span class="sxs-lookup"><span data-stu-id="151f5-335">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="151f5-336">W przeciwnym razie metoda zwraca 200 z treści odpowiedzi JSON.</span><span class="sxs-lookup"><span data-stu-id="151f5-336">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="151f5-337">Zwracanie `item` wyników w odpowiedzi HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="151f5-337">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="151f5-338">Metoda PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="151f5-338">The PutTodoItem method</span></span>

<span data-ttu-id="151f5-339">Zbadaj `PutTodoItem` metodę:</span><span class="sxs-lookup"><span data-stu-id="151f5-339">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="151f5-340">`PutTodoItem`jest podobny `PostTodoItem`do , z tą różnicą, że używa HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="151f5-340">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="151f5-341">Odpowiedź to [204 (Brak zawartości).](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)</span><span class="sxs-lookup"><span data-stu-id="151f5-341">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="151f5-342">Zgodnie ze specyfikacją HTTP żądanie PUT wymaga od klienta wysłania całej zaktualizowanej jednostki, a nie tylko zmian.</span><span class="sxs-lookup"><span data-stu-id="151f5-342">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="151f5-343">Aby obsługiwać częściowe aktualizacje, należy użyć protokołu [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="151f5-343">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="151f5-344">Jeśli wystąpi błąd `PutTodoItem`wywołanie `GET` , wywołanie, aby upewnić się, że istnieje element w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="151f5-344">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="151f5-345">Przetestuj metodę PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="151f5-345">Test the PutTodoItem method</span></span>

<span data-ttu-id="151f5-346">W tym przykładzie użyto bazy danych w pamięci, która musi zostać zainicjowana przy każdym uruchomieniu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="151f5-346">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="151f5-347">Musi istnieć element w bazie danych przed dokonaniem wywołania PUT.</span><span class="sxs-lookup"><span data-stu-id="151f5-347">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="151f5-348">Wywołanie GET, aby upewnić się, że jest element w bazie danych przed wykonaniem wywołania PUT.</span><span class="sxs-lookup"><span data-stu-id="151f5-348">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="151f5-349">Zaktualizuj element do wykonania o identyfikatorze = 1 i ustaw jego nazwę na "feed fish":</span><span class="sxs-lookup"><span data-stu-id="151f5-349">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="151f5-350">Na poniższej ilustracji przedstawiono aktualizację Listonosz:</span><span class="sxs-lookup"><span data-stu-id="151f5-350">The following image shows the Postman update:</span></span>

![Konsola listonosza z odpowiedzią 204 (brak zawartości)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="151f5-352">Metoda DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="151f5-352">The DeleteTodoItem method</span></span>

<span data-ttu-id="151f5-353">Zbadaj `DeleteTodoItem` metodę:</span><span class="sxs-lookup"><span data-stu-id="151f5-353">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="151f5-354">Testowanie metody DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="151f5-354">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="151f5-355">Użyj listonosza, aby usunąć element do wykonania:</span><span class="sxs-lookup"><span data-stu-id="151f5-355">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="151f5-356">Ustaw metodę `DELETE`na .</span><span class="sxs-lookup"><span data-stu-id="151f5-356">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="151f5-357">Ustaw identyfikator URI obiektu do usunięcia `https://localhost:5001/api/TodoItems/1`(na przykład ).</span><span class="sxs-lookup"><span data-stu-id="151f5-357">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="151f5-358">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="151f5-358">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="151f5-359">Zapobieganie nadmiernemu księgowaniu</span><span class="sxs-lookup"><span data-stu-id="151f5-359">Prevent over-posting</span></span>

<span data-ttu-id="151f5-360">Obecnie przykładowa aplikacja udostępnia `TodoItem` cały obiekt.</span><span class="sxs-lookup"><span data-stu-id="151f5-360">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="151f5-361">Aplikacje Productions zazwyczaj ograniczają dane, które są wprowadzane do danych wejściowych i zwracane przy użyciu podzbioru modelu.</span><span class="sxs-lookup"><span data-stu-id="151f5-361">Productions apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="151f5-362">Istnieje wiele powodów, dla których jest to ważne, a bezpieczeństwo jest najważniejsze.</span><span class="sxs-lookup"><span data-stu-id="151f5-362">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="151f5-363">Podzbiór modelu jest zwykle określany jako obiekt transferu danych (DTO), model wejściowy lub model widoku.</span><span class="sxs-lookup"><span data-stu-id="151f5-363">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="151f5-364">**DTO** jest używany w tym artykule.</span><span class="sxs-lookup"><span data-stu-id="151f5-364">**DTO** is used in this article.</span></span>

<span data-ttu-id="151f5-365">DTO może być stosowany do:</span><span class="sxs-lookup"><span data-stu-id="151f5-365">A DTO may be used to:</span></span>

* <span data-ttu-id="151f5-366">Zapobiegaj nadmiernemu księgowaniu.</span><span class="sxs-lookup"><span data-stu-id="151f5-366">Prevent over-posting.</span></span>
* <span data-ttu-id="151f5-367">Ukryj właściwości, których klienci nie powinni wyświetlać.</span><span class="sxs-lookup"><span data-stu-id="151f5-367">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="151f5-368">Pomiń niektóre właściwości w celu zmniejszenia rozmiaru ładunku.</span><span class="sxs-lookup"><span data-stu-id="151f5-368">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="151f5-369">Spłaszcz wykresy obiektów zawierających obiekty zagnieżdżone.</span><span class="sxs-lookup"><span data-stu-id="151f5-369">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="151f5-370">Wykresy spłaszczonych obiektów mogą być wygodniejsze dla klientów.</span><span class="sxs-lookup"><span data-stu-id="151f5-370">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="151f5-371">Aby zademonstrować podejście DTO, zaktualizuj klasę, `TodoItem` aby uwzględniła tajne pole:</span><span class="sxs-lookup"><span data-stu-id="151f5-371">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="151f5-372">Pole tajne musi być ukryte przed tą aplikacją, ale aplikacja administracyjna może go udostępnić.</span><span class="sxs-lookup"><span data-stu-id="151f5-372">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="151f5-373">Sprawdź, czy możesz zaksięgować i uzyskać tajne pole.</span><span class="sxs-lookup"><span data-stu-id="151f5-373">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="151f5-374">Utwórz model DTO:</span><span class="sxs-lookup"><span data-stu-id="151f5-374">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="151f5-375">Zaktualizuj `TodoItemsController` do użycia: `TodoItemDTO`</span><span class="sxs-lookup"><span data-stu-id="151f5-375">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="151f5-376">Sprawdź, czy nie możesz zaksięgować ani nie uzyskać pola tajnego.</span><span class="sxs-lookup"><span data-stu-id="151f5-376">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="151f5-377">Wywoływanie internetowego interfejsu API za pomocą języka JavaScript</span><span class="sxs-lookup"><span data-stu-id="151f5-377">Call the web API with JavaScript</span></span>

<span data-ttu-id="151f5-378">Zobacz [Samouczek: Wywołanie ASP.NET core internet API z JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="151f5-378">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="151f5-379">Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="151f5-379">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="151f5-380">Tworzenie projektu internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="151f5-380">Create a web API project.</span></span>
> * <span data-ttu-id="151f5-381">Dodaj klasę modelu i kontekst bazy danych.</span><span class="sxs-lookup"><span data-stu-id="151f5-381">Add a model class and a database context.</span></span>
> * <span data-ttu-id="151f5-382">Dodaj kontroler.</span><span class="sxs-lookup"><span data-stu-id="151f5-382">Add a controller.</span></span>
> * <span data-ttu-id="151f5-383">Dodaj metody CRUD.</span><span class="sxs-lookup"><span data-stu-id="151f5-383">Add CRUD methods.</span></span>
> * <span data-ttu-id="151f5-384">Konfigurowanie ścieżek routingu i adresów URL.</span><span class="sxs-lookup"><span data-stu-id="151f5-384">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="151f5-385">Określ wartości zwracane.</span><span class="sxs-lookup"><span data-stu-id="151f5-385">Specify return values.</span></span>
> * <span data-ttu-id="151f5-386">Wywołanie internetowego interfejsu API za pomocą listonosza.</span><span class="sxs-lookup"><span data-stu-id="151f5-386">Call the web API with Postman.</span></span>
> * <span data-ttu-id="151f5-387">Wywołanie internetowego interfejsu API za pomocą języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="151f5-387">Call the web API with JavaScript.</span></span>

<span data-ttu-id="151f5-388">Na końcu masz internetowy interfejs API, który może zarządzać elementami "do wykonania" przechowywanymi w relacyjnej bazie danych.</span><span class="sxs-lookup"><span data-stu-id="151f5-388">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="151f5-389">Omówienie</span><span class="sxs-lookup"><span data-stu-id="151f5-389">Overview</span></span>

<span data-ttu-id="151f5-390">W tym samouczku utworzy się następujący interfejs API:</span><span class="sxs-lookup"><span data-stu-id="151f5-390">This tutorial creates the following API:</span></span>

|<span data-ttu-id="151f5-391">interfejs API</span><span class="sxs-lookup"><span data-stu-id="151f5-391">API</span></span> | <span data-ttu-id="151f5-392">Opis</span><span class="sxs-lookup"><span data-stu-id="151f5-392">Description</span></span> | <span data-ttu-id="151f5-393">Treść żądania</span><span class="sxs-lookup"><span data-stu-id="151f5-393">Request body</span></span> | <span data-ttu-id="151f5-394">Treść odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="151f5-394">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="151f5-395">POBIERZ /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="151f5-395">GET /api/TodoItems</span></span> | <span data-ttu-id="151f5-396">Pobierz wszystkie przedmioty do zrobienia</span><span class="sxs-lookup"><span data-stu-id="151f5-396">Get all to-do items</span></span> | <span data-ttu-id="151f5-397">Brak</span><span class="sxs-lookup"><span data-stu-id="151f5-397">None</span></span> | <span data-ttu-id="151f5-398">Tablica elementów do wykonania</span><span class="sxs-lookup"><span data-stu-id="151f5-398">Array of to-do items</span></span>|
|<span data-ttu-id="151f5-399">POBIERZ /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="151f5-399">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="151f5-400">Uzyskaj przedmiot według identyfikatora</span><span class="sxs-lookup"><span data-stu-id="151f5-400">Get an item by ID</span></span> | <span data-ttu-id="151f5-401">Brak</span><span class="sxs-lookup"><span data-stu-id="151f5-401">None</span></span> | <span data-ttu-id="151f5-402">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="151f5-402">To-do item</span></span>|
|<span data-ttu-id="151f5-403">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="151f5-403">POST /api/TodoItems</span></span> | <span data-ttu-id="151f5-404">Dodawanie nowego elementu</span><span class="sxs-lookup"><span data-stu-id="151f5-404">Add a new item</span></span> | <span data-ttu-id="151f5-405">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="151f5-405">To-do item</span></span> | <span data-ttu-id="151f5-406">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="151f5-406">To-do item</span></span> |
|<span data-ttu-id="151f5-407">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="151f5-407">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="151f5-408">Aktualizowanie istniejącego elementu&nbsp;</span><span class="sxs-lookup"><span data-stu-id="151f5-408">Update an existing item &nbsp;</span></span> | <span data-ttu-id="151f5-409">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="151f5-409">To-do item</span></span> | <span data-ttu-id="151f5-410">Brak</span><span class="sxs-lookup"><span data-stu-id="151f5-410">None</span></span> |
|<span data-ttu-id="151f5-411">USUŃ /api/TodoItems/{id} &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="151f5-411">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="151f5-412">Usuwanie elementu &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="151f5-412">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="151f5-413">Brak</span><span class="sxs-lookup"><span data-stu-id="151f5-413">None</span></span> | <span data-ttu-id="151f5-414">Brak</span><span class="sxs-lookup"><span data-stu-id="151f5-414">None</span></span>|

<span data-ttu-id="151f5-415">Na poniższym diagramie przedstawiono projekt aplikacji.</span><span class="sxs-lookup"><span data-stu-id="151f5-415">The following diagram shows the design of the app.</span></span>

![Klient jest reprezentowany przez pole po lewej stronie.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="151f5-421">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="151f5-421">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="151f5-422">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="151f5-422">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="151f5-423">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="151f5-423">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="151f5-424">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="151f5-424">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="151f5-425">Tworzenie projektu sieci Web</span><span class="sxs-lookup"><span data-stu-id="151f5-425">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="151f5-426">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="151f5-426">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="151f5-427">Z menu **Plik** wybierz polecenie **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="151f5-427">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="151f5-428">Wybierz szablon **ASP.NET Core Web Application** i kliknij przycisk **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="151f5-428">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="151f5-429">Nazwij projekt *TodoApi* i kliknij przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="151f5-429">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="151f5-430">W oknie **dialogowym Tworzenie nowej ASP.NET core web application** upewnij się, że wybrano opcję **.NET Core** i ASP.NET Core **2.2.**</span><span class="sxs-lookup"><span data-stu-id="151f5-430">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="151f5-431">Wybierz szablon **interfejsu API** i kliknij przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="151f5-431">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="151f5-432">**Nie wybieraj** opcji **Włącz obsługę platformy Docker**.</span><span class="sxs-lookup"><span data-stu-id="151f5-432">**Don't** select **Enable Docker Support**.</span></span>

![Vs nowe okno dialogowe projektu](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="151f5-434">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="151f5-434">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="151f5-435">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="151f5-435">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="151f5-436">Zmień katalogi`cd`( ) na folder, który będzie zawierał folder projektu.</span><span class="sxs-lookup"><span data-stu-id="151f5-436">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="151f5-437">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="151f5-437">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="151f5-438">Te polecenia tworzą nowy projekt interfejsu API sieci web i otwierają nowe wystąpienie programu Visual Studio Code w nowym folderze projektu.</span><span class="sxs-lookup"><span data-stu-id="151f5-438">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="151f5-439">Gdy w oknie dialogowym pojawi się pytanie, czy chcesz dodać wymagane zasoby do projektu, wybierz opcję **Tak**.</span><span class="sxs-lookup"><span data-stu-id="151f5-439">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="151f5-440">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="151f5-440">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="151f5-441">Wybierz **pozycję Plik** > **nowego rozwiązania**.</span><span class="sxs-lookup"><span data-stu-id="151f5-441">Select **File** > **New Solution**.</span></span>

  ![macOS Nowe rozwiązanie](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="151f5-443">Wybierz następnie **pozycję .NET Core** > **App** > **API** > **.**</span><span class="sxs-lookup"><span data-stu-id="151f5-443">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS Nowe okno dialogowe projektu](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="151f5-445">W oknie dialogowym **Konfigurowanie nowego interfejsu API sieci Web ASP.NET Core** zaakceptuj domyślną **ramę docelową** \**.NET Core 2.2*.</span><span class="sxs-lookup"><span data-stu-id="151f5-445">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="151f5-446">Wprowadź *pozycję TodoApi* dla **nazwy projektu,** a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="151f5-446">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![okno dialogowe konfiguracji](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="151f5-448">Testowanie interfejsu API</span><span class="sxs-lookup"><span data-stu-id="151f5-448">Test the API</span></span>

<span data-ttu-id="151f5-449">Szablon projektu tworzy `values` interfejs API.</span><span class="sxs-lookup"><span data-stu-id="151f5-449">The project template creates a `values` API.</span></span> <span data-ttu-id="151f5-450">Wywołanie `Get` metody z przeglądarki, aby przetestować aplikację.</span><span class="sxs-lookup"><span data-stu-id="151f5-450">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="151f5-451">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="151f5-451">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="151f5-452">Naciśnij klawisze Ctrl+F5, aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="151f5-452">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="151f5-453">Visual Studio uruchamia przeglądarkę `https://localhost:<port>/api/values`i `<port>` przechodzi do , gdzie jest losowo wybrany numer portu.</span><span class="sxs-lookup"><span data-stu-id="151f5-453">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="151f5-454">Jeśli pojawi się okno dialogowe z pytaniem, czy certyfikat IIS Express ma być zaufany, wybierz opcję **Tak**.</span><span class="sxs-lookup"><span data-stu-id="151f5-454">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="151f5-455">W oknie dialogowym **Ostrzeżenie o zabezpieczeniach,** które pojawi się obok, wybierz pozycję **Tak**.</span><span class="sxs-lookup"><span data-stu-id="151f5-455">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="151f5-456">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="151f5-456">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="151f5-457">Naciśnij klawisze Ctrl+F5, aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="151f5-457">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="151f5-458">W przeglądarce przejdź do `https://localhost:5001/api/values`następującego adresu URL: .</span><span class="sxs-lookup"><span data-stu-id="151f5-458">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="151f5-459">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="151f5-459">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="151f5-460">Wybierz **uruchom** > **debugowanie startowe,** aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="151f5-460">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="151f5-461">Program Visual Studio dla komputerów `https://localhost:<port>`Mac `<port>` uruchamia przeglądarkę i przechodzi do miejsca, w którym jest losowo wybrany numer portu.</span><span class="sxs-lookup"><span data-stu-id="151f5-461">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="151f5-462">Zwracany jest błąd HTTP 404 (nie znaleziono).</span><span class="sxs-lookup"><span data-stu-id="151f5-462">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="151f5-463">Dołącz `/api/values` do adresu URL (zmień `https://localhost:<port>/api/values`adres URL na ).</span><span class="sxs-lookup"><span data-stu-id="151f5-463">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="151f5-464">Zostanie zwrócony następujący kod JSON:</span><span class="sxs-lookup"><span data-stu-id="151f5-464">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="151f5-465">Dodawanie klasy modelu</span><span class="sxs-lookup"><span data-stu-id="151f5-465">Add a model class</span></span>

<span data-ttu-id="151f5-466">*Model* to zestaw klas, które reprezentują dane, którymi zarządza aplikacja.</span><span class="sxs-lookup"><span data-stu-id="151f5-466">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="151f5-467">Model dla tej aplikacji `TodoItem` jest pojedynczą klasą.</span><span class="sxs-lookup"><span data-stu-id="151f5-467">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="151f5-468">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="151f5-468">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="151f5-469">W **Eksploratorze rozwiązań**kliknij projekt prawym przyciskiem myszy.</span><span class="sxs-lookup"><span data-stu-id="151f5-469">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="151f5-470">Wybierz **pozycję Dodaj** > **nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="151f5-470">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="151f5-471">Nazwij folder *Models*.</span><span class="sxs-lookup"><span data-stu-id="151f5-471">Name the folder *Models*.</span></span>

* <span data-ttu-id="151f5-472">Kliknij prawym przyciskiem myszy folder *Modele* i wybierz polecenie **Dodaj** > **klasę**.</span><span class="sxs-lookup"><span data-stu-id="151f5-472">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="151f5-473">Nazwij klasę *TodoItem* i wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="151f5-473">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="151f5-474">Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="151f5-474">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="151f5-475">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="151f5-475">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="151f5-476">Dodawanie folderu o nazwie *Modele*.</span><span class="sxs-lookup"><span data-stu-id="151f5-476">Add a folder named *Models*.</span></span>

* <span data-ttu-id="151f5-477">Dodaj `TodoItem` klasę do folderu *Models* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="151f5-477">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="151f5-478">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="151f5-478">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="151f5-479">Kliknij prawym przyciskiem myszy projekt.</span><span class="sxs-lookup"><span data-stu-id="151f5-479">Right-click the project.</span></span> <span data-ttu-id="151f5-480">Wybierz **pozycję Dodaj** > **nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="151f5-480">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="151f5-481">Nazwij folder *Models*.</span><span class="sxs-lookup"><span data-stu-id="151f5-481">Name the folder *Models*.</span></span>

  ![nowy folder](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="151f5-483">Kliknij prawym przyciskiem myszy folder *Modele* i wybierz polecenie **Dodaj** > **nową** > **klasę opróżnienia ogólnego** > **Empty Class**pliku .</span><span class="sxs-lookup"><span data-stu-id="151f5-483">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="151f5-484">Nazwij klasę *TodoItem*, a następnie kliknij przycisk **Nowy**.</span><span class="sxs-lookup"><span data-stu-id="151f5-484">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="151f5-485">Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="151f5-485">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="151f5-486">Właściwość `Id` działa jako unikatowy klucz w relacyjnej bazie danych.</span><span class="sxs-lookup"><span data-stu-id="151f5-486">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="151f5-487">Klasy modelu można przejść w dowolnym miejscu w projekcie, ale *Models* folder jest używany przez konwencję.</span><span class="sxs-lookup"><span data-stu-id="151f5-487">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="151f5-488">Dodawanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="151f5-488">Add a database context</span></span>

<span data-ttu-id="151f5-489">*Kontekst bazy danych* jest klasą główną, która koordynuje funkcjonalność entity framework dla modelu danych.</span><span class="sxs-lookup"><span data-stu-id="151f5-489">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="151f5-490">Ta klasa jest tworzona `Microsoft.EntityFrameworkCore.DbContext` przez wyprowadzanie z klasy.</span><span class="sxs-lookup"><span data-stu-id="151f5-490">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="151f5-491">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="151f5-491">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="151f5-492">Kliknij prawym przyciskiem myszy folder *Modele* i wybierz polecenie **Dodaj** > **klasę**.</span><span class="sxs-lookup"><span data-stu-id="151f5-492">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="151f5-493">Nazwij klasę *TodoContext* i kliknij przycisk **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="151f5-493">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="151f5-494">Kod programu Visual Studio / Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="151f5-494">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="151f5-495">Dodaj `TodoContext` klasę do folderu *Modele.*</span><span class="sxs-lookup"><span data-stu-id="151f5-495">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="151f5-496">Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="151f5-496">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="151f5-497">Rejestrowanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="151f5-497">Register the database context</span></span>

<span data-ttu-id="151f5-498">W ASP.NET Core usługi, takie jak kontekst bazy danych, muszą być zarejestrowane w kontenerze [iniekcji zależności (DI).](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="151f5-498">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="151f5-499">Kontener zapewnia usługę do kontrolerów.</span><span class="sxs-lookup"><span data-stu-id="151f5-499">The container provides the service to controllers.</span></span>

<span data-ttu-id="151f5-500">Zaktualizuj *Startup.cs* następującym wyróżnionym kodem:</span><span class="sxs-lookup"><span data-stu-id="151f5-500">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="151f5-501">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="151f5-501">The preceding code:</span></span>

* <span data-ttu-id="151f5-502">Usuwa nieużywane `using` deklaracje.</span><span class="sxs-lookup"><span data-stu-id="151f5-502">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="151f5-503">Dodaje kontekst bazy danych do kontenera DI.</span><span class="sxs-lookup"><span data-stu-id="151f5-503">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="151f5-504">Określa, że kontekst bazy danych będzie używany w bazie danych w pamięci.</span><span class="sxs-lookup"><span data-stu-id="151f5-504">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="151f5-505">Dodawanie kontrolera</span><span class="sxs-lookup"><span data-stu-id="151f5-505">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="151f5-506">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="151f5-506">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="151f5-507">Kliknij prawym przyciskiem myszy folder *Kontrolery.*</span><span class="sxs-lookup"><span data-stu-id="151f5-507">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="151f5-508">Wybierz **pozycję Dodaj** > **nowy element**.</span><span class="sxs-lookup"><span data-stu-id="151f5-508">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="151f5-509">W oknie dialogowym **Dodawanie nowego elementu** wybierz szablon klasy **kontrolera interfejsu API.**</span><span class="sxs-lookup"><span data-stu-id="151f5-509">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="151f5-510">Nazwij klasę *TodoController*i wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="151f5-510">Name the class *TodoController*, and select **Add**.</span></span>

  ![Dodawanie nowego okna dialogowego Element z kontrolerem w polu wyszukiwania i wybranym kontrolerem interfejsu API sieci Web](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="151f5-512">Kod programu Visual Studio / Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="151f5-512">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="151f5-513">W folderze *Kontrolery* utwórz `TodoController`klasę o nazwie .</span><span class="sxs-lookup"><span data-stu-id="151f5-513">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="151f5-514">Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="151f5-514">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="151f5-515">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="151f5-515">The preceding code:</span></span>

* <span data-ttu-id="151f5-516">Definiuje klasę kontrolera interfejsu API bez metod.</span><span class="sxs-lookup"><span data-stu-id="151f5-516">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="151f5-517">Oznacza klasę atrybutem. [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute)</span><span class="sxs-lookup"><span data-stu-id="151f5-517">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="151f5-518">Ten atrybut wskazuje, że kontroler odpowiada na żądania interfejsu API sieci web.</span><span class="sxs-lookup"><span data-stu-id="151f5-518">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="151f5-519">Aby uzyskać informacje o określonych zachowaniach, <xref:web-api/index>które umożliwia atrybut, zobacz .</span><span class="sxs-lookup"><span data-stu-id="151f5-519">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="151f5-520">Używa DI, aby wstrzyknąć kontekst bazy danych (`TodoContext`) do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="151f5-520">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="151f5-521">Kontekst bazy danych jest używany w każdej z metod [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) w kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="151f5-521">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="151f5-522">Dodaje element `Item1` o nazwie do bazy danych, jeśli baza danych jest pusta.</span><span class="sxs-lookup"><span data-stu-id="151f5-522">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="151f5-523">Ten kod znajduje się w konstruktorze, więc jest uruchamiany za każdym razem, gdy jest nowe żądanie HTTP.</span><span class="sxs-lookup"><span data-stu-id="151f5-523">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="151f5-524">Jeśli usuniesz wszystkie elementy, konstruktor tworzy `Item1` ponownie następnym razem, gdy wywoływana jest metoda interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="151f5-524">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="151f5-525">Więc może to wyglądać usunięcie nie działa, gdy rzeczywiście nie działa.</span><span class="sxs-lookup"><span data-stu-id="151f5-525">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="151f5-526">Dodaj metody Pobierz</span><span class="sxs-lookup"><span data-stu-id="151f5-526">Add Get methods</span></span>

<span data-ttu-id="151f5-527">Aby zapewnić interfejs API, który pobiera elementy do wykonania, dodaj do `TodoController` klasy następujące metody:</span><span class="sxs-lookup"><span data-stu-id="151f5-527">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="151f5-528">Te metody implementują dwa punkty końcowe GET:</span><span class="sxs-lookup"><span data-stu-id="151f5-528">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="151f5-529">Zatrzymaj aplikację, jeśli jest nadal uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="151f5-529">Stop the app if it's still running.</span></span> <span data-ttu-id="151f5-530">Następnie uruchom go ponownie, aby uwzględnić najnowsze zmiany.</span><span class="sxs-lookup"><span data-stu-id="151f5-530">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="151f5-531">Przetestuj aplikację, wywołując dwa punkty końcowe z przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="151f5-531">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="151f5-532">Przykład:</span><span class="sxs-lookup"><span data-stu-id="151f5-532">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="151f5-533">Następująca odpowiedź HTTP jest wywoływana przez wywołanie: `GetTodoItems`</span><span class="sxs-lookup"><span data-stu-id="151f5-533">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="151f5-534">Ścieżki routingu i adresów URL</span><span class="sxs-lookup"><span data-stu-id="151f5-534">Routing and URL paths</span></span>

<span data-ttu-id="151f5-535">Atrybut [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) oznacza metodę, która odpowiada na żądanie HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="151f5-535">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="151f5-536">Ścieżka adresu URL dla każdej metody jest skonstruowana w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="151f5-536">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="151f5-537">Zacznij od ciągu szablonu w `Route` atrybucie kontrolera:</span><span class="sxs-lookup"><span data-stu-id="151f5-537">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="151f5-538">Zamień `[controller]` na nazwę kontrolera, który zgodnie z konwencją jest nazwą klasy kontrolera minus sufiks "Controller".</span><span class="sxs-lookup"><span data-stu-id="151f5-538">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="151f5-539">W tym przykładzie nazwa klasy kontrolera to **Kontroler Dodo,** więc nazwa kontrolera jest "todo".</span><span class="sxs-lookup"><span data-stu-id="151f5-539">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="151f5-540">ASP.NET [Routingu rdzenia](xref:mvc/controllers/routing) jest niewrażliwe na wielkości liter.</span><span class="sxs-lookup"><span data-stu-id="151f5-540">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="151f5-541">Jeśli `[HttpGet]` atrybut ma szablon trasy (na `[HttpGet("products")]`przykład ), dołącz go do ścieżki.</span><span class="sxs-lookup"><span data-stu-id="151f5-541">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="151f5-542">W tym przykładzie nie jest używany szablon.</span><span class="sxs-lookup"><span data-stu-id="151f5-542">This sample doesn't use a template.</span></span> <span data-ttu-id="151f5-543">Aby uzyskać więcej informacji, zobacz [Routing atrybutów z atrybutami Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="151f5-543">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="151f5-544">W poniższej `GetTodoItem` `"{id}"` metodzie jest zmienną zastępczą dla unikatowego identyfikatora elementu do wykonania.</span><span class="sxs-lookup"><span data-stu-id="151f5-544">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="151f5-545">Po `GetTodoItem` wywołaniu wartość `"{id}"` w adresie URL jest dostarczana`id` do metody w jego parametrze.</span><span class="sxs-lookup"><span data-stu-id="151f5-545">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="151f5-546">Zwracane wartości</span><span class="sxs-lookup"><span data-stu-id="151f5-546">Return values</span></span>

<span data-ttu-id="151f5-547">Typ zwracany `GetTodoItems` i `GetTodoItem` metody jest [ActionResult\<T> typu](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="151f5-547">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="151f5-548">ASP.NET Core automatycznie serializuje obiekt do [JSON](https://www.json.org/) i zapisuje JSON w treści komunikatu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="151f5-548">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="151f5-549">Kod odpowiedzi dla tego typu zwracania wynosi 200, przy założeniu, że nie ma żadnych nieobsługiwał wyjątków.</span><span class="sxs-lookup"><span data-stu-id="151f5-549">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="151f5-550">Nieobsługiwały się wyjątkami są tłumaczone na błędy 5xx.</span><span class="sxs-lookup"><span data-stu-id="151f5-550">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="151f5-551">`ActionResult`zwracane typy mogą reprezentować szeroki zakres kodów stanu HTTP.</span><span class="sxs-lookup"><span data-stu-id="151f5-551">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="151f5-552">Na przykład `GetTodoItem` może zwrócić dwie różne wartości stanu:</span><span class="sxs-lookup"><span data-stu-id="151f5-552">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="151f5-553">Jeśli żaden element nie pasuje do żądanego identyfikatora, metoda zwraca kod błędu [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) 404.</span><span class="sxs-lookup"><span data-stu-id="151f5-553">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="151f5-554">W przeciwnym razie metoda zwraca 200 z treści odpowiedzi JSON.</span><span class="sxs-lookup"><span data-stu-id="151f5-554">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="151f5-555">Zwracanie `item` wyników w odpowiedzi HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="151f5-555">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="151f5-556">Przetestuj metodę GetTodoItems</span><span class="sxs-lookup"><span data-stu-id="151f5-556">Test the GetTodoItems method</span></span>

<span data-ttu-id="151f5-557">Ten samouczek używa postmana do testowania internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="151f5-557">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="151f5-558">Zainstaluj [Listonosz](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="151f5-558">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="151f5-559">Uruchom aplikację internetową.</span><span class="sxs-lookup"><span data-stu-id="151f5-559">Start the web app.</span></span>
* <span data-ttu-id="151f5-560">Rozpocznij postman.</span><span class="sxs-lookup"><span data-stu-id="151f5-560">Start Postman.</span></span>
* <span data-ttu-id="151f5-561">Wyłącz **weryfikację certyfikatu SSL**.</span><span class="sxs-lookup"><span data-stu-id="151f5-561">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="151f5-562">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="151f5-562">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="151f5-563">W **obszarze** > **Ustawienia** plików (karta**Ogólne)** wyłącz **weryfikację certyfikatu SSL**.</span><span class="sxs-lookup"><span data-stu-id="151f5-563">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="151f5-564">Kod programu Visual Studio / Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="151f5-564">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="151f5-565">Z **preferencji listonosza** > **Preferences** (karta**Ogólne)** wyłącz **weryfikację certyfikatu SSL**.</span><span class="sxs-lookup"><span data-stu-id="151f5-565">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="151f5-566">Możesz też wybrać klucz i wybrać **ustawienia**, a następnie wyłączyć weryfikację certyfikatu SSL.</span><span class="sxs-lookup"><span data-stu-id="151f5-566">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="151f5-567">Ponownie włącz weryfikację certyfikatu SSL po przetestowaniu sterownika.</span><span class="sxs-lookup"><span data-stu-id="151f5-567">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="151f5-568">Utwórz nowe żądanie.</span><span class="sxs-lookup"><span data-stu-id="151f5-568">Create a new request.</span></span>
  * <span data-ttu-id="151f5-569">Ustaw metodę HTTP na **GET**.</span><span class="sxs-lookup"><span data-stu-id="151f5-569">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="151f5-570">Ustaw adres URL `https://localhost:<port>/api/todo`żądania na .</span><span class="sxs-lookup"><span data-stu-id="151f5-570">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="151f5-571">Na przykład `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="151f5-571">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="151f5-572">Ustaw **widok dwóch okienek** w postmanie.</span><span class="sxs-lookup"><span data-stu-id="151f5-572">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="151f5-573">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="151f5-573">Select **Send**.</span></span>

![Listonosz z prośbą o uzyskanie](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="151f5-575">Dodawanie metody tworzenia</span><span class="sxs-lookup"><span data-stu-id="151f5-575">Add a Create method</span></span>

<span data-ttu-id="151f5-576">Dodaj następującą `PostTodoItem` metodę wewnątrz *controllers/TodoController.cs*:</span><span class="sxs-lookup"><span data-stu-id="151f5-576">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="151f5-577">Poprzedni kod jest http post metody, zgodnie [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) z atrybutem.</span><span class="sxs-lookup"><span data-stu-id="151f5-577">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="151f5-578">Metoda pobiera wartość elementu do wykonania z treści żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="151f5-578">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="151f5-579">Metoda: `CreatedAtAction`</span><span class="sxs-lookup"><span data-stu-id="151f5-579">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="151f5-580">Zwraca kod stanu HTTP 201, jeśli zakończy się pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="151f5-580">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="151f5-581">HTTP 201 jest standardową odpowiedzią dla metody HTTP POST, która tworzy nowy zasób na serwerze.</span><span class="sxs-lookup"><span data-stu-id="151f5-581">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="151f5-582">Dodaje `Location` nagłówek do odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="151f5-582">Adds a `Location` header to the response.</span></span> <span data-ttu-id="151f5-583">Nagłówek `Location` określa identyfikator URI nowo utworzonego elementu do wykonania.</span><span class="sxs-lookup"><span data-stu-id="151f5-583">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="151f5-584">Aby uzyskać więcej informacji, zobacz [10.2.2 201 Utworzono](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="151f5-584">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="151f5-585">Odwołuje się `GetTodoItem` do akcji, aby utworzyć `Location` identyfikator URI nagłówka.</span><span class="sxs-lookup"><span data-stu-id="151f5-585">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="151f5-586">C# `nameof` słowo kluczowe jest używany w celu uniknięcia `CreatedAtAction` twardego kodowania nazwę akcji w wywołaniu.</span><span class="sxs-lookup"><span data-stu-id="151f5-586">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="151f5-587">Przetestuj metodę PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="151f5-587">Test the PostTodoItem method</span></span>

* <span data-ttu-id="151f5-588">Skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="151f5-588">Build the project.</span></span>
* <span data-ttu-id="151f5-589">W u wysłać metodę `POST`Http na .</span><span class="sxs-lookup"><span data-stu-id="151f5-589">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="151f5-590">Wybierz kartę **Treść**.</span><span class="sxs-lookup"><span data-stu-id="151f5-590">Select the **Body** tab.</span></span>
* <span data-ttu-id="151f5-591">Wybierz **nieprzetworzony** przycisk radiowy.</span><span class="sxs-lookup"><span data-stu-id="151f5-591">Select the **raw** radio button.</span></span>
* <span data-ttu-id="151f5-592">Ustaw typ na **JSON (application/json)**.</span><span class="sxs-lookup"><span data-stu-id="151f5-592">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="151f5-593">W treści żądania wprowadź JSON dla elementu do wykonania:</span><span class="sxs-lookup"><span data-stu-id="151f5-593">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="151f5-594">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="151f5-594">Select **Send**.</span></span>

  ![Listonosz z żądaniem utworzenia](first-web-api/_static/create.png)

  <span data-ttu-id="151f5-596">Jeśli otrzymasz błąd 405 Metoda niedozwolone, prawdopodobnie jest wynikiem nie kompilacji projektu po dodaniu `PostTodoItem` metody.</span><span class="sxs-lookup"><span data-stu-id="151f5-596">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="151f5-597">Testowanie identyfikatora URI nagłówka lokalizacji</span><span class="sxs-lookup"><span data-stu-id="151f5-597">Test the location header URI</span></span>

* <span data-ttu-id="151f5-598">Wybierz kartę **Nagłówki** w okienku **Odpowiedzi.**</span><span class="sxs-lookup"><span data-stu-id="151f5-598">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="151f5-599">Skopiuj wartość **nagłówka Lokalizacja:**</span><span class="sxs-lookup"><span data-stu-id="151f5-599">Copy the **Location** header value:</span></span>

  ![Karta Nagłówki konsoli Listonosz](first-web-api/_static/pmc2.png)

* <span data-ttu-id="151f5-601">Ustaw metodę na GET.</span><span class="sxs-lookup"><span data-stu-id="151f5-601">Set the method to GET.</span></span>
* <span data-ttu-id="151f5-602">Wklej identyfikator URI (na przykład `https://localhost:5001/api/Todo/2`).</span><span class="sxs-lookup"><span data-stu-id="151f5-602">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="151f5-603">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="151f5-603">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="151f5-604">Dodawanie metody PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="151f5-604">Add a PutTodoItem method</span></span>

<span data-ttu-id="151f5-605">Dodaj następującą `PutTodoItem` metodę:</span><span class="sxs-lookup"><span data-stu-id="151f5-605">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="151f5-606">`PutTodoItem`jest podobny `PostTodoItem`do , z tą różnicą, że używa HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="151f5-606">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="151f5-607">Odpowiedź to [204 (Brak zawartości).](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)</span><span class="sxs-lookup"><span data-stu-id="151f5-607">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="151f5-608">Zgodnie ze specyfikacją HTTP żądanie PUT wymaga od klienta wysłania całej zaktualizowanej jednostki, a nie tylko zmian.</span><span class="sxs-lookup"><span data-stu-id="151f5-608">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="151f5-609">Aby obsługiwać częściowe aktualizacje, należy użyć protokołu [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="151f5-609">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="151f5-610">Jeśli wystąpi błąd `PutTodoItem`wywołanie `GET` , wywołanie, aby upewnić się, że istnieje element w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="151f5-610">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="151f5-611">Przetestuj metodę PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="151f5-611">Test the PutTodoItem method</span></span>

<span data-ttu-id="151f5-612">W tym przykładzie użyto bazy danych w pamięci, która musi zostać zainicjowana przy każdym uruchomieniu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="151f5-612">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="151f5-613">Musi istnieć element w bazie danych przed dokonaniem wywołania PUT.</span><span class="sxs-lookup"><span data-stu-id="151f5-613">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="151f5-614">Wywołanie GET, aby upewnić się, że jest element w bazie danych przed wykonaniem wywołania PUT.</span><span class="sxs-lookup"><span data-stu-id="151f5-614">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="151f5-615">Zaktualizuj element do wykonania, który ma identyfikator = 1 i ustaw jego nazwę na "feed fish":</span><span class="sxs-lookup"><span data-stu-id="151f5-615">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="151f5-616">Na poniższej ilustracji przedstawiono aktualizację Listonosz:</span><span class="sxs-lookup"><span data-stu-id="151f5-616">The following image shows the Postman update:</span></span>

![Konsola listonosza z odpowiedzią 204 (brak zawartości)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="151f5-618">Dodawanie metody DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="151f5-618">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="151f5-619">Dodaj następującą `DeleteTodoItem` metodę:</span><span class="sxs-lookup"><span data-stu-id="151f5-619">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="151f5-620">Odpowiedź `DeleteTodoItem` to [204 (Brak zawartości).](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)</span><span class="sxs-lookup"><span data-stu-id="151f5-620">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="151f5-621">Testowanie metody DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="151f5-621">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="151f5-622">Użyj listonosza, aby usunąć element do wykonania:</span><span class="sxs-lookup"><span data-stu-id="151f5-622">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="151f5-623">Ustaw metodę `DELETE`na .</span><span class="sxs-lookup"><span data-stu-id="151f5-623">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="151f5-624">Ustaw identyfikator URI obiektu do usunięcia `https://localhost:5001/api/todo/1`(na przykład ).</span><span class="sxs-lookup"><span data-stu-id="151f5-624">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="151f5-625">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="151f5-625">Select **Send**.</span></span>

<span data-ttu-id="151f5-626">Przykładowa aplikacja umożliwia usunięcie wszystkich elementów.</span><span class="sxs-lookup"><span data-stu-id="151f5-626">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="151f5-627">Jednak po usunięciu ostatniego elementu, nowy jest tworzony przez konstruktora klasy modelu przy następnym wywołaniu interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="151f5-627">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="151f5-628">Wywoływanie internetowego interfejsu API za pomocą języka JavaScript</span><span class="sxs-lookup"><span data-stu-id="151f5-628">Call the web API with JavaScript</span></span>

<span data-ttu-id="151f5-629">W tej sekcji zostanie dodana strona HTML, która używa języka JavaScript do wywoływania internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="151f5-629">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="151f5-630">jQuery inicjuje żądanie.</span><span class="sxs-lookup"><span data-stu-id="151f5-630">jQuery initiates the request.</span></span> <span data-ttu-id="151f5-631">JavaScript aktualizuje stronę ze szczegółami z odpowiedzi internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="151f5-631">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="151f5-632">Skonfiguruj aplikację do [obsługi plików statycznych](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) i [włącz domyślne mapowanie plików,](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) aktualizując *Startup.cs* za pomocą następującego podświetlanych kodów:</span><span class="sxs-lookup"><span data-stu-id="151f5-632">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="151f5-633">Utwórz folder *wwwroot* w katalogu projektu.</span><span class="sxs-lookup"><span data-stu-id="151f5-633">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="151f5-634">Dodaj plik HTML o nazwie *index.html* do katalogu *wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="151f5-634">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="151f5-635">Zastąp jego zawartość następującym znacznikiem:</span><span class="sxs-lookup"><span data-stu-id="151f5-635">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="151f5-636">Dodaj plik JavaScript o nazwie *site.js* do katalogu *wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="151f5-636">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="151f5-637">Zastąp jego zawartość następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="151f5-637">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="151f5-638">Aby przetestować stronę HTML lokalnie, może być wymagana zmiana ustawień uruchamiania projektu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="151f5-638">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="151f5-639">Otwórz *właściwości\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="151f5-639">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="151f5-640">Usuń `launchUrl` właściwość, aby wymusić otwarcie aplikacji w *pliku index.html*&mdash;domyślnego pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="151f5-640">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="151f5-641">W tym przykładzie wywołuje wszystkie metody CRUD interfejsu API sieci web.</span><span class="sxs-lookup"><span data-stu-id="151f5-641">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="151f5-642">Poniżej przedstawiono objaśnienia wywołań interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="151f5-642">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="151f5-643">Wyświetlanie listy elementów do wykonania</span><span class="sxs-lookup"><span data-stu-id="151f5-643">Get a list of to-do items</span></span>

<span data-ttu-id="151f5-644">jQuery wysyła żądanie HTTP GET do internetowego interfejsu API, który zwraca JSON reprezentujący tablicę elementów do wykonania.</span><span class="sxs-lookup"><span data-stu-id="151f5-644">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="151f5-645">Funkcja `success` wywołania zwrotnego jest wywoływana, jeśli żądanie zakończy się pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="151f5-645">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="151f5-646">W wywołaniu zwrotnym dom jest aktualizowany o informacje do wykonania.</span><span class="sxs-lookup"><span data-stu-id="151f5-646">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="151f5-647">Dodawanie elementu do wykonania</span><span class="sxs-lookup"><span data-stu-id="151f5-647">Add a to-do item</span></span>

<span data-ttu-id="151f5-648">jQuery wysyła żądanie HTTP POST z elementem do wykonania w treści żądania.</span><span class="sxs-lookup"><span data-stu-id="151f5-648">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="151f5-649">Opcje `accepts` `contentType` i opcje `application/json` są ustawione tak, aby określić typ nośnika odbierany i wysyłany.</span><span class="sxs-lookup"><span data-stu-id="151f5-649">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="151f5-650">Element do wykonania jest konwertowany na JSON przy użyciu pliku [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="151f5-650">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="151f5-651">Gdy interfejs API zwraca kod `getData` stanu pomyślnego, funkcja jest wywoływana w celu zaktualizowania tabeli HTML.</span><span class="sxs-lookup"><span data-stu-id="151f5-651">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="151f5-652">Aktualizowanie elementu do wykonania</span><span class="sxs-lookup"><span data-stu-id="151f5-652">Update a to-do item</span></span>

<span data-ttu-id="151f5-653">Aktualizowanie elementu do wykonania jest podobne do dodawania jednego.</span><span class="sxs-lookup"><span data-stu-id="151f5-653">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="151f5-654">Zmiany, `url` aby dodać unikatowy identyfikator elementu, `type` `PUT`a jest .</span><span class="sxs-lookup"><span data-stu-id="151f5-654">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="151f5-655">Usuwanie elementu do wykonania</span><span class="sxs-lookup"><span data-stu-id="151f5-655">Delete a to-do item</span></span>

<span data-ttu-id="151f5-656">Usunięcie elementu do wykonania odbywa się poprzez `type` ustawienie wywołania `DELETE` AJAX i określenie unikatowego identyfikatora elementu w adresie URL.</span><span class="sxs-lookup"><span data-stu-id="151f5-656">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="151f5-657">Dodawanie obsługi uwierzytelniania do internetowego interfejsu API</span><span class="sxs-lookup"><span data-stu-id="151f5-657">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a><span data-ttu-id="151f5-658">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="151f5-658">Additional resources</span></span>

<span data-ttu-id="151f5-659">[Wyświetl lub pobierz przykładowy kod dla tego samouczka](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="151f5-659">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="151f5-660">Zobacz, [jak pobrać](xref:index#how-to-download-a-sample)plik .</span><span class="sxs-lookup"><span data-stu-id="151f5-660">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="151f5-661">Więcej informacji zawierają następujące zasoby:</span><span class="sxs-lookup"><span data-stu-id="151f5-661">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="151f5-662">Wersja tego samouczka w YouTube</span><span class="sxs-lookup"><span data-stu-id="151f5-662">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
