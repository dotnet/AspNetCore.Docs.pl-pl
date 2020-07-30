---
title: 'Samouczek: Tworzenie internetowego interfejsu API za pomocą ASP.NET Core'
author: rick-anderson
description: Dowiedz się, jak utworzyć internetowy interfejs API za pomocą ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 2/25/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-web-api
ms.openlocfilehash: 2383934070a65b8131e890a170186b736d3fcec0
ms.sourcegitcommit: 5a36758cca2861aeb10840093e46d273a6e6e91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "86869994"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="23d74-103">Samouczek: Tworzenie internetowego interfejsu API za pomocą ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="23d74-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="23d74-104">Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirka Larkin](https://twitter.com/serpent5)i [Jan Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="23d74-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="23d74-105">Ten samouczek uczy się podstaw tworzenia interfejsu API sieci Web za pomocą ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="23d74-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="23d74-106">Ten samouczek zawiera informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="23d74-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="23d74-107">Utwórz projekt interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="23d74-107">Create a web API project.</span></span>
> * <span data-ttu-id="23d74-108">Dodaj klasę modelu i kontekst bazy danych.</span><span class="sxs-lookup"><span data-stu-id="23d74-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="23d74-109">Tworzy szkielet kontrolera z metodami CRUD.</span><span class="sxs-lookup"><span data-stu-id="23d74-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="23d74-110">Skonfiguruj Routing, ścieżki URL i wartości zwracane.</span><span class="sxs-lookup"><span data-stu-id="23d74-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="23d74-111">Wywołaj interfejs API sieci Web za pomocą programu Poster.</span><span class="sxs-lookup"><span data-stu-id="23d74-111">Call the web API with Postman.</span></span>

<span data-ttu-id="23d74-112">Na końcu znajduje się internetowy interfejs API, który może zarządzać elementami do wykonania przechowywanymi w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="23d74-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="23d74-113">Omówienie</span><span class="sxs-lookup"><span data-stu-id="23d74-113">Overview</span></span>

<span data-ttu-id="23d74-114">Ten samouczek tworzy następujący interfejs API:</span><span class="sxs-lookup"><span data-stu-id="23d74-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="23d74-115">Interfejs API</span><span class="sxs-lookup"><span data-stu-id="23d74-115">API</span></span> | <span data-ttu-id="23d74-116">Opis</span><span class="sxs-lookup"><span data-stu-id="23d74-116">Description</span></span> | <span data-ttu-id="23d74-117">Treść żądania</span><span class="sxs-lookup"><span data-stu-id="23d74-117">Request body</span></span> | <span data-ttu-id="23d74-118">Treść odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="23d74-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="23d74-119">Pobierz wszystkie elementy do wykonania</span><span class="sxs-lookup"><span data-stu-id="23d74-119">Get all to-do items</span></span> | <span data-ttu-id="23d74-120">Brak</span><span class="sxs-lookup"><span data-stu-id="23d74-120">None</span></span> | <span data-ttu-id="23d74-121">Tablica elementów do wykonania</span><span class="sxs-lookup"><span data-stu-id="23d74-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="23d74-122">Pobieranie elementu według identyfikatora</span><span class="sxs-lookup"><span data-stu-id="23d74-122">Get an item by ID</span></span> | <span data-ttu-id="23d74-123">Brak</span><span class="sxs-lookup"><span data-stu-id="23d74-123">None</span></span> | <span data-ttu-id="23d74-124">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="23d74-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="23d74-125">Dodaj nowy element</span><span class="sxs-lookup"><span data-stu-id="23d74-125">Add a new item</span></span> | <span data-ttu-id="23d74-126">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="23d74-126">To-do item</span></span> | <span data-ttu-id="23d74-127">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="23d74-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="23d74-128">Aktualizowanie istniejącego elementu&nbsp;</span><span class="sxs-lookup"><span data-stu-id="23d74-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="23d74-129">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="23d74-129">To-do item</span></span> | <span data-ttu-id="23d74-130">Brak</span><span class="sxs-lookup"><span data-stu-id="23d74-130">None</span></span> |
|<span data-ttu-id="23d74-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="23d74-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="23d74-132">Usuń element &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="23d74-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="23d74-133">Brak</span><span class="sxs-lookup"><span data-stu-id="23d74-133">None</span></span> | <span data-ttu-id="23d74-134">Brak</span><span class="sxs-lookup"><span data-stu-id="23d74-134">None</span></span>|

<span data-ttu-id="23d74-135">Na poniższym diagramie przedstawiono projekt aplikacji.</span><span class="sxs-lookup"><span data-stu-id="23d74-135">The following diagram shows the design of the app.</span></span>

![Klient jest reprezentowany przez pole po lewej stronie.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="23d74-141">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="23d74-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="23d74-142">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="23d74-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="23d74-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="23d74-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="23d74-144">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="23d74-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="23d74-145">Tworzenie projektu sieci Web</span><span class="sxs-lookup"><span data-stu-id="23d74-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="23d74-146">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="23d74-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="23d74-147">Z menu **plik** wybierz pozycję **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="23d74-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="23d74-148">Wybierz szablon **aplikacja sieci Web ASP.NET Core** a następnie kliknij przycisk **dalej**.</span><span class="sxs-lookup"><span data-stu-id="23d74-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="23d74-149">Nazwij projekt *TodoApi* i kliknij pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="23d74-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="23d74-150">W oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** upewnij się, że wybrano opcję **.net Core** i **ASP.NET Core 3,1** .</span><span class="sxs-lookup"><span data-stu-id="23d74-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="23d74-151">Wybierz szablon **interfejsu API** i kliknij przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="23d74-151">Select the **API** template and click **Create**.</span></span>

![Okno dialogowe programu VS New Project](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="23d74-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="23d74-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="23d74-154">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="23d74-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="23d74-155">Zmień katalog ( `cd` ) do folderu, który będzie zawierać folder projektu.</span><span class="sxs-lookup"><span data-stu-id="23d74-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="23d74-156">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="23d74-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="23d74-157">Gdy zostanie wyświetlone okno dialogowe z pytaniem, czy chcesz dodać wymagane zasoby do projektu, wybierz opcję **tak**.</span><span class="sxs-lookup"><span data-stu-id="23d74-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="23d74-158">Poprzednie polecenia:</span><span class="sxs-lookup"><span data-stu-id="23d74-158">The preceding commands:</span></span>

  * <span data-ttu-id="23d74-159">Tworzy nowy projekt internetowego interfejsu API i otwiera go w Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="23d74-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="23d74-160">Dodaje pakiety NuGet, które są wymagane w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="23d74-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="23d74-161">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="23d74-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="23d74-162">Wybierz pozycję **plik** > **nowe rozwiązanie**.</span><span class="sxs-lookup"><span data-stu-id="23d74-162">Select **File** > **New Solution**.</span></span>

  ![macOS nowe rozwiązanie](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="23d74-164">W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz **.NET Core**pozycję  >  **App**  >  **interfejs API**aplikacji .NET Core  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="23d74-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="23d74-165">W wersji 8,6 lub nowszej wybierz pozycję **Web and Console**  >  **App**  >  **interfejs API**  >  **Next** aplikacji sieci Web i konsoli.</span><span class="sxs-lookup"><span data-stu-id="23d74-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next** .</span></span>

  ![Wybór szablonu interfejsu API macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="23d74-167">W oknie dialogowym **Konfigurowanie nowego interfejsu API sieci Web ASP.NET Core** wybierz najnowszą platformę **docelową**.NET Core 3. x.</span><span class="sxs-lookup"><span data-stu-id="23d74-167">In the **Configure your new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="23d74-168">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="23d74-168">Select **Next**.</span></span>

* <span data-ttu-id="23d74-169">Wprowadź *TodoApi* jako **nazwę projektu** , a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="23d74-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![okno dialogowe konfiguracji](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="23d74-171">Otwórz Terminal poleceń w folderze projektu i uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="23d74-171">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="23d74-172">Testowanie interfejsu API</span><span class="sxs-lookup"><span data-stu-id="23d74-172">Test the API</span></span>

<span data-ttu-id="23d74-173">Szablon projektu tworzy `WeatherForecast` interfejs API.</span><span class="sxs-lookup"><span data-stu-id="23d74-173">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="23d74-174">Wywołaj `Get` metodę z przeglądarki, aby przetestować aplikację.</span><span class="sxs-lookup"><span data-stu-id="23d74-174">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="23d74-175">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="23d74-175">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="23d74-176">Naciśnij klawisze CTRL + F5, aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="23d74-176">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="23d74-177">Program Visual Studio uruchamia przeglądarkę i przechodzi do `https://localhost:<port>/WeatherForecast` lokalizacji, gdzie `<port>` jest losowo wybierany numer portu.</span><span class="sxs-lookup"><span data-stu-id="23d74-177">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="23d74-178">Jeśli zostanie wyświetlone okno dialogowe z pytaniem, czy należy zaufać certyfikatowi IIS Express, wybierz pozycję **tak**.</span><span class="sxs-lookup"><span data-stu-id="23d74-178">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="23d74-179">W wyświetlonym oknie dialogowym **ostrzeżenia o zabezpieczeniach** wybierz pozycję **tak**.</span><span class="sxs-lookup"><span data-stu-id="23d74-179">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="23d74-180">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="23d74-180">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="23d74-181">Naciśnij klawisze CTRL + F5, aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="23d74-181">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="23d74-182">W przeglądarce przejdź do następującego adresu URL: `https://localhost:5001/WeatherForecast` .</span><span class="sxs-lookup"><span data-stu-id="23d74-182">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="23d74-183">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="23d74-183">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="23d74-184">Wybierz pozycję **Uruchom**  >  **Rozpocznij debugowanie** , aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="23d74-184">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="23d74-185">Visual Studio dla komputerów Mac uruchamia przeglądarkę i nawiguje do `https://localhost:<port>` , gdzie `<port>` jest losowo wybranym numerem portu.</span><span class="sxs-lookup"><span data-stu-id="23d74-185">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="23d74-186">Zwracany jest błąd HTTP 404 (nie znaleziono).</span><span class="sxs-lookup"><span data-stu-id="23d74-186">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="23d74-187">Dołącz `/WeatherForecast` do adresu URL (Zmień adres URL na `https://localhost:<port>/WeatherForecast` ).</span><span class="sxs-lookup"><span data-stu-id="23d74-187">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="23d74-188">Zwracany jest kod JSON podobny do następującego:</span><span class="sxs-lookup"><span data-stu-id="23d74-188">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="23d74-189">Dodaj klasę modelu</span><span class="sxs-lookup"><span data-stu-id="23d74-189">Add a model class</span></span>

<span data-ttu-id="23d74-190">*Model* to zestaw klas, które reprezentują dane zarządzane przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="23d74-190">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="23d74-191">Model tej aplikacji jest pojedynczą `TodoItem` klasą.</span><span class="sxs-lookup"><span data-stu-id="23d74-191">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="23d74-192">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="23d74-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="23d74-193">W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt.</span><span class="sxs-lookup"><span data-stu-id="23d74-193">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="23d74-194">Wybierz pozycję **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="23d74-194">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="23d74-195">Nazwij *modele*folderów.</span><span class="sxs-lookup"><span data-stu-id="23d74-195">Name the folder *Models*.</span></span>

* <span data-ttu-id="23d74-196">Kliknij prawym przyciskiem myszy folder *modele* i wybierz polecenie **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="23d74-196">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="23d74-197">Nadaj klasie nazwę *TodoItem* i wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="23d74-197">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="23d74-198">Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="23d74-198">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="23d74-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="23d74-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="23d74-200">Dodaj folder o nazwie *models*.</span><span class="sxs-lookup"><span data-stu-id="23d74-200">Add a folder named *Models*.</span></span>

* <span data-ttu-id="23d74-201">Dodaj `TodoItem` klasę do folderu *models* o następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="23d74-201">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="23d74-202">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="23d74-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="23d74-203">Kliknij prawym przyciskiem myszy projekt.</span><span class="sxs-lookup"><span data-stu-id="23d74-203">Right-click the project.</span></span> <span data-ttu-id="23d74-204">Wybierz pozycję **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="23d74-204">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="23d74-205">Nazwij *modele*folderów.</span><span class="sxs-lookup"><span data-stu-id="23d74-205">Name the folder *Models*.</span></span>

  ![Nowy folder](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="23d74-207">Kliknij prawym przyciskiem myszy folder *modele* , a następnie wybierz pozycję **Dodaj** > **nowy plik** > **ogólna** > **pusta Klasa**.</span><span class="sxs-lookup"><span data-stu-id="23d74-207">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="23d74-208">Nazwij klasę *TodoItem*, a następnie kliknij pozycję **New (nowy**).</span><span class="sxs-lookup"><span data-stu-id="23d74-208">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="23d74-209">Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="23d74-209">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="23d74-210">`Id`Właściwość działa jako unikatowy klucz w relacyjnej bazie danych.</span><span class="sxs-lookup"><span data-stu-id="23d74-210">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="23d74-211">Klasy modelu mogą przejść do dowolnego miejsca w projekcie, ale folder *modele* jest używany przez Konwencję.</span><span class="sxs-lookup"><span data-stu-id="23d74-211">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="23d74-212">Dodawanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="23d74-212">Add a database context</span></span>

<span data-ttu-id="23d74-213">*Kontekst bazy danych* jest główną klasą, która koordynuje Entity Framework funkcji dla modelu danych.</span><span class="sxs-lookup"><span data-stu-id="23d74-213">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="23d74-214">Ta klasa jest tworzona przez wyprowadzanie z `Microsoft.EntityFrameworkCore.DbContext` klasy.</span><span class="sxs-lookup"><span data-stu-id="23d74-214">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="23d74-215">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="23d74-215">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="23d74-216">Dodawanie pakietów NuGet</span><span class="sxs-lookup"><span data-stu-id="23d74-216">Add NuGet packages</span></span>

* <span data-ttu-id="23d74-217">W menu **Narzędzia** wybierz pozycję **menedżer pakietów NuGet > zarządzanie pakietami NuGet dla rozwiązania**.</span><span class="sxs-lookup"><span data-stu-id="23d74-217">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="23d74-218">Wybierz kartę **Przeglądaj** , a następnie w polu wyszukiwania wprowadź ciąg **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="23d74-218">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="23d74-219">W lewym okienku wybierz pozycję **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="23d74-219">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="23d74-220">Zaznacz pole wyboru **projekt** w prawym okienku, a następnie wybierz pozycję **Zainstaluj**.</span><span class="sxs-lookup"><span data-stu-id="23d74-220">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="23d74-221">Aby dodać pakiet NuGet, użyj powyższych instrukcji `Microsoft.EntityFrameworkCore.InMemory` .</span><span class="sxs-lookup"><span data-stu-id="23d74-221">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![Menedżer pakietów NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="23d74-223">Dodawanie kontekstu bazy danych TodoContext</span><span class="sxs-lookup"><span data-stu-id="23d74-223">Add the TodoContext database context</span></span>

* <span data-ttu-id="23d74-224">Kliknij prawym przyciskiem myszy folder *modele* i wybierz polecenie **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="23d74-224">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="23d74-225">Nadaj klasie nazwę *TodoContext* i kliknij przycisk **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="23d74-225">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="23d74-226">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="23d74-226">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="23d74-227">Dodaj `TodoContext` klasę do folderu *models* .</span><span class="sxs-lookup"><span data-stu-id="23d74-227">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="23d74-228">Wprowadź następujący kod:</span><span class="sxs-lookup"><span data-stu-id="23d74-228">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="23d74-229">Rejestrowanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="23d74-229">Register the database context</span></span>

<span data-ttu-id="23d74-230">W ASP.NET Core usługi, takie jak kontekst bazy danych, muszą być zarejestrowane z kontenerem [iniekcji zależności (di)](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="23d74-230">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="23d74-231">Kontener udostępnia usługę kontrolerom.</span><span class="sxs-lookup"><span data-stu-id="23d74-231">The container provides the service to controllers.</span></span>

<span data-ttu-id="23d74-232">Zaktualizuj *Startup.cs* o następujący wyróżniony kod:</span><span class="sxs-lookup"><span data-stu-id="23d74-232">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="23d74-233">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="23d74-233">The preceding code:</span></span>

* <span data-ttu-id="23d74-234">Usuwa nieużywane `using` deklaracje.</span><span class="sxs-lookup"><span data-stu-id="23d74-234">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="23d74-235">Dodaje kontekst bazy danych do kontenera DI.</span><span class="sxs-lookup"><span data-stu-id="23d74-235">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="23d74-236">Określa, że kontekst bazy danych będzie używać bazy danych w pamięci.</span><span class="sxs-lookup"><span data-stu-id="23d74-236">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="23d74-237">Tworzenie szkieletu kontrolera</span><span class="sxs-lookup"><span data-stu-id="23d74-237">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="23d74-238">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="23d74-238">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="23d74-239">Kliknij prawym przyciskiem myszy folder *controllers* .</span><span class="sxs-lookup"><span data-stu-id="23d74-239">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="23d74-240">Wybierz pozycję **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="23d74-240">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="23d74-241">Wybierz pozycję **kontroler interfejsu API z akcjami, używając Entity Framework**, a następnie wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="23d74-241">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="23d74-242">Na stronie **Dodawanie kontrolera interfejsu API z akcjami przy użyciu Entity Framework** dialogowego:</span><span class="sxs-lookup"><span data-stu-id="23d74-242">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="23d74-243">Wybierz pozycję **TodoItem (TodoApi. models)** w **klasie model**.</span><span class="sxs-lookup"><span data-stu-id="23d74-243">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="23d74-244">W **klasie kontekstu danych**wybierz pozycję **TodoContext (TodoApi. models)** .</span><span class="sxs-lookup"><span data-stu-id="23d74-244">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="23d74-245">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="23d74-245">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="23d74-246">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="23d74-246">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="23d74-247">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="23d74-247">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="23d74-248">Poprzednie polecenia:</span><span class="sxs-lookup"><span data-stu-id="23d74-248">The preceding commands:</span></span>

* <span data-ttu-id="23d74-249">Dodaj pakiety NuGet wymagane do tworzenia szkieletów.</span><span class="sxs-lookup"><span data-stu-id="23d74-249">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="23d74-250">Instaluje aparat tworzenia szkieletu ( `dotnet-aspnet-codegenerator` ).</span><span class="sxs-lookup"><span data-stu-id="23d74-250">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="23d74-251">Szkielety `TodoItemsController` .</span><span class="sxs-lookup"><span data-stu-id="23d74-251">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="23d74-252">Wygenerowany kod:</span><span class="sxs-lookup"><span data-stu-id="23d74-252">The generated code:</span></span>

* <span data-ttu-id="23d74-253">Oznacza klasę [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) atrybutem.</span><span class="sxs-lookup"><span data-stu-id="23d74-253">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="23d74-254">Ten atrybut wskazuje, że kontroler odpowiada na żądania interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="23d74-254">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="23d74-255">Aby uzyskać informacje o określonych zachowaniach, które włącza atrybut, zobacz <xref:web-api/index> .</span><span class="sxs-lookup"><span data-stu-id="23d74-255">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="23d74-256">Używa funkcji DI do iniekcji kontekstu bazy danych ( `TodoContext` ) do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="23d74-256">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="23d74-257">Kontekst bazy danych jest używany w każdej z metod [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) w kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="23d74-257">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="23d74-258">Szablony ASP.NET Core dla:</span><span class="sxs-lookup"><span data-stu-id="23d74-258">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="23d74-259">Kontrolery z widokami obejmują `[action]` szablon trasy.</span><span class="sxs-lookup"><span data-stu-id="23d74-259">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="23d74-260">Kontrolery interfejsu API nie należą `[action]` do szablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="23d74-260">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="23d74-261">Gdy `[action]` token nie znajduje się w szablonie trasy, nazwa [akcji](xref:mvc/controllers/routing#action) jest wykluczona z trasy.</span><span class="sxs-lookup"><span data-stu-id="23d74-261">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="23d74-262">Oznacza to, że nazwa metody skojarzonej z akcją nie jest używana w zgodnej trasie.</span><span class="sxs-lookup"><span data-stu-id="23d74-262">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="23d74-263">Badanie metody PostTodoItem Create</span><span class="sxs-lookup"><span data-stu-id="23d74-263">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="23d74-264">Zastąp instrukcję return w, `PostTodoItem` Aby użyć operatora [nameof](/dotnet/csharp/language-reference/operators/nameof) :</span><span class="sxs-lookup"><span data-stu-id="23d74-264">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="23d74-265">Poprzedni kod jest metodą POST protokołu HTTP, jak wskazano w [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) atrybucie.</span><span class="sxs-lookup"><span data-stu-id="23d74-265">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="23d74-266">Metoda pobiera wartość elementu do wykonania z treści żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="23d74-266">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="23d74-267"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>Metoda:</span><span class="sxs-lookup"><span data-stu-id="23d74-267">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="23d74-268">W razie powodzenia zwraca kod stanu HTTP 201.</span><span class="sxs-lookup"><span data-stu-id="23d74-268">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="23d74-269">HTTP 201 to standardowa odpowiedź dla metody POST protokołu HTTP, która tworzy nowy zasób na serwerze.</span><span class="sxs-lookup"><span data-stu-id="23d74-269">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="23d74-270">Dodaje nagłówek [lokalizacji](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) do odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="23d74-270">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="23d74-271">`Location`Nagłówek określa [Identyfikator URI](https://developer.mozilla.org/docs/Glossary/URI) nowo utworzonego elementu do wykonania.</span><span class="sxs-lookup"><span data-stu-id="23d74-271">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="23d74-272">Aby uzyskać więcej informacji, zobacz [10.2.2 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="23d74-272">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="23d74-273">Odwołuje się do `GetTodoItem` akcji tworzenia `Location` identyfikatora URI nagłówka.</span><span class="sxs-lookup"><span data-stu-id="23d74-273">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="23d74-274">`nameof`Słowo kluczowe języka C# służy do zapobiegania twardemu kodowaniu nazwy akcji w `CreatedAtAction` wywołaniu.</span><span class="sxs-lookup"><span data-stu-id="23d74-274">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="23d74-275">Zainstaluj program Poster</span><span class="sxs-lookup"><span data-stu-id="23d74-275">Install Postman</span></span>

<span data-ttu-id="23d74-276">Ten samouczek używa programu do testowania interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="23d74-276">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="23d74-277">Zainstaluj program [Poster](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="23d74-277">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="23d74-278">Uruchom aplikację internetową.</span><span class="sxs-lookup"><span data-stu-id="23d74-278">Start the web app.</span></span>
* <span data-ttu-id="23d74-279">Uruchom wpis.</span><span class="sxs-lookup"><span data-stu-id="23d74-279">Start Postman.</span></span>
* <span data-ttu-id="23d74-280">Wyłącz **weryfikację certyfikatu SSL**</span><span class="sxs-lookup"><span data-stu-id="23d74-280">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="23d74-281">W **File** obszarze > **Ustawienia** pliku (karta**Ogólne** ) Wyłącz **weryfikację certyfikatu SSL**.</span><span class="sxs-lookup"><span data-stu-id="23d74-281">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="23d74-282">Po przetestowaniu kontrolera ponownie Włącz weryfikację certyfikatu SSL.</span><span class="sxs-lookup"><span data-stu-id="23d74-282">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="23d74-283">Test PostTodoItem za pomocą programu Poster</span><span class="sxs-lookup"><span data-stu-id="23d74-283">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="23d74-284">Utwórz nowe żądanie.</span><span class="sxs-lookup"><span data-stu-id="23d74-284">Create a new request.</span></span>
* <span data-ttu-id="23d74-285">Ustaw metodę HTTP na `POST` .</span><span class="sxs-lookup"><span data-stu-id="23d74-285">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="23d74-286">Wybierz kartę **Treść**.</span><span class="sxs-lookup"><span data-stu-id="23d74-286">Select the **Body** tab.</span></span>
* <span data-ttu-id="23d74-287">Wybierz przycisk radiowy **RAW** .</span><span class="sxs-lookup"><span data-stu-id="23d74-287">Select the **raw** radio button.</span></span>
* <span data-ttu-id="23d74-288">Ustaw typ na **JSON (Application/JSON)**.</span><span class="sxs-lookup"><span data-stu-id="23d74-288">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="23d74-289">W treści żądania wprowadź kod JSON dla elementu do wykonania:</span><span class="sxs-lookup"><span data-stu-id="23d74-289">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="23d74-290">Wybierz pozycję **Send** (Wyślij).</span><span class="sxs-lookup"><span data-stu-id="23d74-290">Select **Send**.</span></span>

  ![Ogłoś przy użyciu żądania Create](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="23d74-292">Testowanie identyfikatora URI nagłówka lokalizacji</span><span class="sxs-lookup"><span data-stu-id="23d74-292">Test the location header URI</span></span>

* <span data-ttu-id="23d74-293">Wybierz kartę **nagłówki** w okienku **odpowiedź** .</span><span class="sxs-lookup"><span data-stu-id="23d74-293">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="23d74-294">Skopiuj wartość nagłówka **lokalizacji** :</span><span class="sxs-lookup"><span data-stu-id="23d74-294">Copy the **Location** header value:</span></span>

  ![Karta nagłówki w konsoli programu Poster](first-web-api/_static/3/create.png)

* <span data-ttu-id="23d74-296">Ustaw metodę, aby uzyskać.</span><span class="sxs-lookup"><span data-stu-id="23d74-296">Set the method to GET.</span></span>
* <span data-ttu-id="23d74-297">Wklej URI (na przykład `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="23d74-297">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="23d74-298">Wybierz pozycję **Send** (Wyślij).</span><span class="sxs-lookup"><span data-stu-id="23d74-298">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="23d74-299">Badanie metod GET</span><span class="sxs-lookup"><span data-stu-id="23d74-299">Examine the GET methods</span></span>

<span data-ttu-id="23d74-300">Te metody implementują dwa punkty końcowe GET:</span><span class="sxs-lookup"><span data-stu-id="23d74-300">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="23d74-301">Przetestuj aplikację, wywołując dwa punkty końcowe z przeglądarki lub wpisu.</span><span class="sxs-lookup"><span data-stu-id="23d74-301">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="23d74-302">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="23d74-302">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="23d74-303">Odpowiedź podobna do poniższego jest generowana przez wywołanie `GetTodoItems` :</span><span class="sxs-lookup"><span data-stu-id="23d74-303">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="23d74-304">Test get przy użyciu programu Poster</span><span class="sxs-lookup"><span data-stu-id="23d74-304">Test Get with Postman</span></span>

* <span data-ttu-id="23d74-305">Utwórz nowe żądanie.</span><span class="sxs-lookup"><span data-stu-id="23d74-305">Create a new request.</span></span>
* <span data-ttu-id="23d74-306">Ustaw metodę HTTP, aby **uzyskać**.</span><span class="sxs-lookup"><span data-stu-id="23d74-306">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="23d74-307">Ustaw adres URL żądania `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="23d74-307">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="23d74-308">Na przykład `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="23d74-308">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="23d74-309">Ustaw **dwa widoki okienka** w programie Poster.</span><span class="sxs-lookup"><span data-stu-id="23d74-309">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="23d74-310">Wybierz pozycję **Send** (Wyślij).</span><span class="sxs-lookup"><span data-stu-id="23d74-310">Select **Send**.</span></span>

<span data-ttu-id="23d74-311">Ta aplikacja używa bazy danych w pamięci.</span><span class="sxs-lookup"><span data-stu-id="23d74-311">This app uses an in-memory database.</span></span> <span data-ttu-id="23d74-312">Jeśli aplikacja zostanie zatrzymana i uruchomiona, poprzednie żądanie GET nie zwróci żadnych danych.</span><span class="sxs-lookup"><span data-stu-id="23d74-312">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="23d74-313">Jeśli nie zostaną zwrócone żadne dane, [Opublikuj](#post) dane w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="23d74-313">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="23d74-314">Ścieżki routingu i adresów URL</span><span class="sxs-lookup"><span data-stu-id="23d74-314">Routing and URL paths</span></span>

<span data-ttu-id="23d74-315">Ten [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) atrybut oznacza metodę, która reaguje na żądanie HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="23d74-315">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="23d74-316">Ścieżka adresu URL dla każdej metody jest zbudowana w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="23d74-316">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="23d74-317">Zacznij od ciągu szablonu w `Route` atrybucie kontrolera:</span><span class="sxs-lookup"><span data-stu-id="23d74-317">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="23d74-318">Zastąp `[controller]` nazwą kontrolera, którą Konwencją jest nazwa klasy kontrolera minus sufiks "Controller".</span><span class="sxs-lookup"><span data-stu-id="23d74-318">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="23d74-319">Dla tego przykładu nazwa klasy kontrolera to **TodoItems**Controller, więc nazwa kontrolera to "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="23d74-319">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="23d74-320">W ASP.NET Core [routingu](xref:mvc/controllers/routing) jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="23d74-320">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="23d74-321">Jeśli `[HttpGet]` atrybut ma szablon trasy (na przykład `[HttpGet("products")]` ), Dodaj go do ścieżki.</span><span class="sxs-lookup"><span data-stu-id="23d74-321">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="23d74-322">Ten przykład nie używa szablonu.</span><span class="sxs-lookup"><span data-stu-id="23d74-322">This sample doesn't use a template.</span></span> <span data-ttu-id="23d74-323">Aby uzyskać więcej informacji, zobacz temat [Routing atrybutów z atrybutami http [Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="23d74-323">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="23d74-324">W poniższej `GetTodoItem` metodzie `"{id}"` jest zmienną zastępczą dla unikatowego identyfikatora elementu do wykonania.</span><span class="sxs-lookup"><span data-stu-id="23d74-324">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="23d74-325">Gdy `GetTodoItem` jest wywoływana, wartość `"{id}"` w adresie URL jest podawana do metody w jej `id` parametrze.</span><span class="sxs-lookup"><span data-stu-id="23d74-325">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="23d74-326">Wartości zwracane</span><span class="sxs-lookup"><span data-stu-id="23d74-326">Return values</span></span>

<span data-ttu-id="23d74-327">Zwracany typ `GetTodoItems` `GetTodoItem` metod i jest [ \<T> typem ActionResult](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="23d74-327">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="23d74-328">ASP.NET Core automatycznie serializować obiektu do [formatu JSON](https://www.json.org/) i zapisuje kod JSON w treści komunikatu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="23d74-328">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="23d74-329">Kod odpowiedzi dla tego typu zwracanego to 200, przy założeniu, że nie istnieją Nieobsłużone wyjątki.</span><span class="sxs-lookup"><span data-stu-id="23d74-329">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="23d74-330">Nieobsłużone wyjątki są tłumaczone na błędy 5xx.</span><span class="sxs-lookup"><span data-stu-id="23d74-330">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="23d74-331">`ActionResult`typy zwracane mogą reprezentować szeroką gamę kodów stanu HTTP.</span><span class="sxs-lookup"><span data-stu-id="23d74-331">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="23d74-332">Na przykład `GetTodoItem` może zwracać dwie różne wartości stanu:</span><span class="sxs-lookup"><span data-stu-id="23d74-332">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="23d74-333">Jeśli żaden element nie jest zgodny z żądanym IDENTYFIKATORem, metoda zwraca 404 kod błędu [NOTFOUND](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) .</span><span class="sxs-lookup"><span data-stu-id="23d74-333">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="23d74-334">W przeciwnym razie metoda zwraca 200 z treścią odpowiedzi JSON.</span><span class="sxs-lookup"><span data-stu-id="23d74-334">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="23d74-335">Zwracanie `item` wyników w odpowiedzi HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="23d74-335">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="23d74-336">Metoda PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="23d74-336">The PutTodoItem method</span></span>

<span data-ttu-id="23d74-337">Przeanalizuj metodę `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="23d74-337">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="23d74-338">`PutTodoItem`jest podobny do `PostTodoItem` , z tą różnicą, że używa protokołu HTTP Put.</span><span class="sxs-lookup"><span data-stu-id="23d74-338">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="23d74-339">Odpowiedź to [204 (brak zawartości)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="23d74-339">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="23d74-340">Zgodnie ze specyfikacją protokołu HTTP żądanie PUT wymaga, aby klient wysłał całą zaktualizowaną jednostkę, a nie tylko te zmiany.</span><span class="sxs-lookup"><span data-stu-id="23d74-340">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="23d74-341">Aby zapewnić obsługę częściowych aktualizacji, użyj [poprawki http](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="23d74-341">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="23d74-342">Jeśli wystąpi błąd podczas wywoływania `PutTodoItem` , wywołaj, `GET` Aby upewnić się, że w bazie danych znajduje się element.</span><span class="sxs-lookup"><span data-stu-id="23d74-342">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="23d74-343">Testowanie metody PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="23d74-343">Test the PutTodoItem method</span></span>

<span data-ttu-id="23d74-344">Ten przykład korzysta z bazy danych w pamięci, która musi zostać zainicjowana za każdym razem, gdy aplikacja zostanie uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="23d74-344">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="23d74-345">Przed wykonaniem wywołania PUT musi istnieć element w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="23d74-345">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="23d74-346">Wywołaj polecenie GET, aby upewnić się, że w bazie danych znajduje się element, przed wykonaniem wywołania PUT.</span><span class="sxs-lookup"><span data-stu-id="23d74-346">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="23d74-347">Zaktualizuj element do wykonania o IDENTYFIKATORze 1 i ustaw jego nazwę na "Źródło danych":</span><span class="sxs-lookup"><span data-stu-id="23d74-347">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="23d74-348">Na poniższej ilustracji przedstawiono aktualizację programu Poster:</span><span class="sxs-lookup"><span data-stu-id="23d74-348">The following image shows the Postman update:</span></span>

![Konsola programu Poster pokazująca odpowiedź 204 (brak zawartości)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="23d74-350">Metoda DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="23d74-350">The DeleteTodoItem method</span></span>

<span data-ttu-id="23d74-351">Przeanalizuj metodę `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="23d74-351">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="23d74-352">Testowanie metody DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="23d74-352">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="23d74-353">Użyj programu Poster, aby usunąć element do wykonania:</span><span class="sxs-lookup"><span data-stu-id="23d74-353">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="23d74-354">Ustaw metodę na `DELETE` .</span><span class="sxs-lookup"><span data-stu-id="23d74-354">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="23d74-355">Ustaw identyfikator URI obiektu do usunięcia (na przykład `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="23d74-355">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="23d74-356">Wybierz pozycję **Send** (Wyślij).</span><span class="sxs-lookup"><span data-stu-id="23d74-356">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="23d74-357">Zapobiegaj za pośrednictwem księgowania</span><span class="sxs-lookup"><span data-stu-id="23d74-357">Prevent over-posting</span></span>

<span data-ttu-id="23d74-358">Obecnie Przykładowa aplikacja uwidacznia cały `TodoItem` obiekt.</span><span class="sxs-lookup"><span data-stu-id="23d74-358">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="23d74-359">Aplikacje produkcyjne zwykle ograniczają dane wejściowe i zwracane przy użyciu podzestawu modelu.</span><span class="sxs-lookup"><span data-stu-id="23d74-359">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="23d74-360">Istnieje wiele powodów związanych z tym, a zabezpieczenia są głównymi.</span><span class="sxs-lookup"><span data-stu-id="23d74-360">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="23d74-361">Podzestaw modelu jest zwykle określany jako obiekt Transfer danych (DTO), model wejściowy lub model widoku.</span><span class="sxs-lookup"><span data-stu-id="23d74-361">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="23d74-362">**DTO** jest używany w tym artykule.</span><span class="sxs-lookup"><span data-stu-id="23d74-362">**DTO** is used in this article.</span></span>

<span data-ttu-id="23d74-363">DTO może służyć do:</span><span class="sxs-lookup"><span data-stu-id="23d74-363">A DTO may be used to:</span></span>

* <span data-ttu-id="23d74-364">Zablokuj nadmierne księgowanie.</span><span class="sxs-lookup"><span data-stu-id="23d74-364">Prevent over-posting.</span></span>
* <span data-ttu-id="23d74-365">Ukryj właściwości, które nie powinny być wyświetlane dla klientów.</span><span class="sxs-lookup"><span data-stu-id="23d74-365">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="23d74-366">Pomiń niektóre właściwości, aby zmniejszyć rozmiar ładunku.</span><span class="sxs-lookup"><span data-stu-id="23d74-366">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="23d74-367">Spłaszcz wykresy obiektów zawierające obiekty zagnieżdżone.</span><span class="sxs-lookup"><span data-stu-id="23d74-367">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="23d74-368">Spłaszczone wykresy obiektów mogą być wygodniejsze dla klientów.</span><span class="sxs-lookup"><span data-stu-id="23d74-368">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="23d74-369">Aby zademonstrować podejście DTO, zaktualizuj `TodoItem` klasę w celu uwzględnienia pola tajnego:</span><span class="sxs-lookup"><span data-stu-id="23d74-369">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="23d74-370">Pole tajne musi być ukryte w tej aplikacji, ale aplikacja administracyjna mogła ją uwidocznić.</span><span class="sxs-lookup"><span data-stu-id="23d74-370">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="23d74-371">Sprawdź, czy można opublikować i pobrać pole tajne.</span><span class="sxs-lookup"><span data-stu-id="23d74-371">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="23d74-372">Utwórz model DTO:</span><span class="sxs-lookup"><span data-stu-id="23d74-372">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="23d74-373">Zaktualizuj, `TodoItemsController` Aby użyć `TodoItemDTO` :</span><span class="sxs-lookup"><span data-stu-id="23d74-373">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="23d74-374">Upewnij się, że nie można opublikować lub pobrać pola tajnego.</span><span class="sxs-lookup"><span data-stu-id="23d74-374">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="23d74-375">Wywoływanie interfejsu API sieci Web przy użyciu języka JavaScript</span><span class="sxs-lookup"><span data-stu-id="23d74-375">Call the web API with JavaScript</span></span>

<span data-ttu-id="23d74-376">Zobacz [Samouczek: wywoływanie interfejsu API sieci web ASP.NET Core przy użyciu języka JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="23d74-376">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="23d74-377">Ten samouczek zawiera informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="23d74-377">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="23d74-378">Utwórz projekt interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="23d74-378">Create a web API project.</span></span>
> * <span data-ttu-id="23d74-379">Dodaj klasę modelu i kontekst bazy danych.</span><span class="sxs-lookup"><span data-stu-id="23d74-379">Add a model class and a database context.</span></span>
> * <span data-ttu-id="23d74-380">Dodaj kontroler.</span><span class="sxs-lookup"><span data-stu-id="23d74-380">Add a controller.</span></span>
> * <span data-ttu-id="23d74-381">Dodaj metody CRUD.</span><span class="sxs-lookup"><span data-stu-id="23d74-381">Add CRUD methods.</span></span>
> * <span data-ttu-id="23d74-382">Skonfiguruj ścieżki routingu i adresów URL.</span><span class="sxs-lookup"><span data-stu-id="23d74-382">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="23d74-383">Określ wartości zwracane.</span><span class="sxs-lookup"><span data-stu-id="23d74-383">Specify return values.</span></span>
> * <span data-ttu-id="23d74-384">Wywołaj interfejs API sieci Web za pomocą programu Poster.</span><span class="sxs-lookup"><span data-stu-id="23d74-384">Call the web API with Postman.</span></span>
> * <span data-ttu-id="23d74-385">Wywołaj interfejs API sieci Web za pomocą języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="23d74-385">Call the web API with JavaScript.</span></span>

<span data-ttu-id="23d74-386">Na końcu znajduje się internetowy interfejs API, który może zarządzać elementami do wykonania przechowywanymi w relacyjnej bazie danych.</span><span class="sxs-lookup"><span data-stu-id="23d74-386">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="23d74-387">Omówienie</span><span class="sxs-lookup"><span data-stu-id="23d74-387">Overview</span></span>

<span data-ttu-id="23d74-388">Ten samouczek tworzy następujący interfejs API:</span><span class="sxs-lookup"><span data-stu-id="23d74-388">This tutorial creates the following API:</span></span>

|<span data-ttu-id="23d74-389">Interfejs API</span><span class="sxs-lookup"><span data-stu-id="23d74-389">API</span></span> | <span data-ttu-id="23d74-390">Opis</span><span class="sxs-lookup"><span data-stu-id="23d74-390">Description</span></span> | <span data-ttu-id="23d74-391">Treść żądania</span><span class="sxs-lookup"><span data-stu-id="23d74-391">Request body</span></span> | <span data-ttu-id="23d74-392">Treść odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="23d74-392">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="23d74-393">Pobierz/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="23d74-393">GET /api/TodoItems</span></span> | <span data-ttu-id="23d74-394">Pobierz wszystkie elementy do wykonania</span><span class="sxs-lookup"><span data-stu-id="23d74-394">Get all to-do items</span></span> | <span data-ttu-id="23d74-395">Brak</span><span class="sxs-lookup"><span data-stu-id="23d74-395">None</span></span> | <span data-ttu-id="23d74-396">Tablica elementów do wykonania</span><span class="sxs-lookup"><span data-stu-id="23d74-396">Array of to-do items</span></span>|
|<span data-ttu-id="23d74-397">Pobierz/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="23d74-397">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="23d74-398">Pobieranie elementu według identyfikatora</span><span class="sxs-lookup"><span data-stu-id="23d74-398">Get an item by ID</span></span> | <span data-ttu-id="23d74-399">Brak</span><span class="sxs-lookup"><span data-stu-id="23d74-399">None</span></span> | <span data-ttu-id="23d74-400">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="23d74-400">To-do item</span></span>|
|<span data-ttu-id="23d74-401">Opublikuj/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="23d74-401">POST /api/TodoItems</span></span> | <span data-ttu-id="23d74-402">Dodaj nowy element</span><span class="sxs-lookup"><span data-stu-id="23d74-402">Add a new item</span></span> | <span data-ttu-id="23d74-403">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="23d74-403">To-do item</span></span> | <span data-ttu-id="23d74-404">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="23d74-404">To-do item</span></span> |
|<span data-ttu-id="23d74-405">Umieść/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="23d74-405">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="23d74-406">Aktualizowanie istniejącego elementu&nbsp;</span><span class="sxs-lookup"><span data-stu-id="23d74-406">Update an existing item &nbsp;</span></span> | <span data-ttu-id="23d74-407">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="23d74-407">To-do item</span></span> | <span data-ttu-id="23d74-408">Brak</span><span class="sxs-lookup"><span data-stu-id="23d74-408">None</span></span> |
|<span data-ttu-id="23d74-409">Usuń/api/TodoItems/{id} &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="23d74-409">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="23d74-410">Usuń element &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="23d74-410">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="23d74-411">Brak</span><span class="sxs-lookup"><span data-stu-id="23d74-411">None</span></span> | <span data-ttu-id="23d74-412">Brak</span><span class="sxs-lookup"><span data-stu-id="23d74-412">None</span></span>|

<span data-ttu-id="23d74-413">Na poniższym diagramie przedstawiono projekt aplikacji.</span><span class="sxs-lookup"><span data-stu-id="23d74-413">The following diagram shows the design of the app.</span></span>

![Klient jest reprezentowany przez pole po lewej stronie.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="23d74-419">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="23d74-419">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="23d74-420">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="23d74-420">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="23d74-421">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="23d74-421">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="23d74-422">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="23d74-422">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="23d74-423">Tworzenie projektu sieci Web</span><span class="sxs-lookup"><span data-stu-id="23d74-423">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="23d74-424">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="23d74-424">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="23d74-425">Z menu **plik** wybierz pozycję **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="23d74-425">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="23d74-426">Wybierz szablon **aplikacja sieci Web ASP.NET Core** a następnie kliknij przycisk **dalej**.</span><span class="sxs-lookup"><span data-stu-id="23d74-426">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="23d74-427">Nazwij projekt *TodoApi* i kliknij pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="23d74-427">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="23d74-428">W oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** upewnij się, że wybrano opcję **.net Core** i **ASP.NET Core 2,2** .</span><span class="sxs-lookup"><span data-stu-id="23d74-428">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="23d74-429">Wybierz szablon **interfejsu API** i kliknij przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="23d74-429">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="23d74-430">**Nie** zaznaczaj opcji **Włącz obsługę platformy Docker**.</span><span class="sxs-lookup"><span data-stu-id="23d74-430">**Don't** select **Enable Docker Support**.</span></span>

![Okno dialogowe programu VS New Project](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="23d74-432">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="23d74-432">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="23d74-433">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="23d74-433">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="23d74-434">Zmień katalog ( `cd` ) do folderu, który będzie zawierać folder projektu.</span><span class="sxs-lookup"><span data-stu-id="23d74-434">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="23d74-435">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="23d74-435">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="23d74-436">Te polecenia tworzą nowy projekt internetowego interfejsu API i otwierają nowe wystąpienie Visual Studio Code w nowym folderze projektu.</span><span class="sxs-lookup"><span data-stu-id="23d74-436">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="23d74-437">Gdy zostanie wyświetlone okno dialogowe z pytaniem, czy chcesz dodać wymagane zasoby do projektu, wybierz opcję **tak**.</span><span class="sxs-lookup"><span data-stu-id="23d74-437">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="23d74-438">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="23d74-438">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="23d74-439">Wybierz pozycję **plik** > **nowe rozwiązanie**.</span><span class="sxs-lookup"><span data-stu-id="23d74-439">Select **File** > **New Solution**.</span></span>

  ![macOS nowe rozwiązanie](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="23d74-441">W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz **.NET Core**pozycję  >  **App**  >  **interfejs API**aplikacji .NET Core  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="23d74-441">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="23d74-442">W wersji 8,6 lub nowszej wybierz pozycję **Web and Console**  >  **App**  >  **interfejs API**  >  **Next**aplikacji sieci Web i konsoli.</span><span class="sxs-lookup"><span data-stu-id="23d74-442">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="23d74-443">W oknie dialogowym **Konfigurowanie nowego interfejsu API sieci Web ASP.NET Core** wybierz najnowszą platformę **docelową**programu .NET Core 2. x.</span><span class="sxs-lookup"><span data-stu-id="23d74-443">In the **Configure your new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="23d74-444">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="23d74-444">Select **Next**.</span></span>

* <span data-ttu-id="23d74-445">Wprowadź *TodoApi* jako **nazwę projektu** , a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="23d74-445">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![okno dialogowe konfiguracji](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="23d74-447">Testowanie interfejsu API</span><span class="sxs-lookup"><span data-stu-id="23d74-447">Test the API</span></span>

<span data-ttu-id="23d74-448">Szablon projektu tworzy `values` interfejs API.</span><span class="sxs-lookup"><span data-stu-id="23d74-448">The project template creates a `values` API.</span></span> <span data-ttu-id="23d74-449">Wywołaj `Get` metodę z przeglądarki, aby przetestować aplikację.</span><span class="sxs-lookup"><span data-stu-id="23d74-449">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="23d74-450">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="23d74-450">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="23d74-451">Naciśnij klawisze CTRL + F5, aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="23d74-451">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="23d74-452">Program Visual Studio uruchamia przeglądarkę i przechodzi do `https://localhost:<port>/api/values` lokalizacji, gdzie `<port>` jest losowo wybierany numer portu.</span><span class="sxs-lookup"><span data-stu-id="23d74-452">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="23d74-453">Jeśli zostanie wyświetlone okno dialogowe z pytaniem, czy należy zaufać certyfikatowi IIS Express, wybierz pozycję **tak**.</span><span class="sxs-lookup"><span data-stu-id="23d74-453">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="23d74-454">W wyświetlonym oknie dialogowym **ostrzeżenia o zabezpieczeniach** wybierz pozycję **tak**.</span><span class="sxs-lookup"><span data-stu-id="23d74-454">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="23d74-455">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="23d74-455">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="23d74-456">Naciśnij klawisze CTRL + F5, aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="23d74-456">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="23d74-457">W przeglądarce przejdź do następującego adresu URL: `https://localhost:5001/api/values` .</span><span class="sxs-lookup"><span data-stu-id="23d74-457">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="23d74-458">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="23d74-458">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="23d74-459">Wybierz pozycję **Uruchom**  >  **Rozpocznij debugowanie** , aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="23d74-459">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="23d74-460">Visual Studio dla komputerów Mac uruchamia przeglądarkę i nawiguje do `https://localhost:<port>` , gdzie `<port>` jest losowo wybranym numerem portu.</span><span class="sxs-lookup"><span data-stu-id="23d74-460">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="23d74-461">Zwracany jest błąd HTTP 404 (nie znaleziono).</span><span class="sxs-lookup"><span data-stu-id="23d74-461">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="23d74-462">Dołącz `/api/values` do adresu URL (Zmień adres URL na `https://localhost:<port>/api/values` ).</span><span class="sxs-lookup"><span data-stu-id="23d74-462">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="23d74-463">Zostanie zwrócony następujący kod JSON:</span><span class="sxs-lookup"><span data-stu-id="23d74-463">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="23d74-464">Dodaj klasę modelu</span><span class="sxs-lookup"><span data-stu-id="23d74-464">Add a model class</span></span>

<span data-ttu-id="23d74-465">*Model* to zestaw klas, które reprezentują dane zarządzane przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="23d74-465">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="23d74-466">Model tej aplikacji jest pojedynczą `TodoItem` klasą.</span><span class="sxs-lookup"><span data-stu-id="23d74-466">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="23d74-467">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="23d74-467">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="23d74-468">W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt.</span><span class="sxs-lookup"><span data-stu-id="23d74-468">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="23d74-469">Wybierz pozycję **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="23d74-469">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="23d74-470">Nazwij *modele*folderów.</span><span class="sxs-lookup"><span data-stu-id="23d74-470">Name the folder *Models*.</span></span>

* <span data-ttu-id="23d74-471">Kliknij prawym przyciskiem myszy folder *modele* i wybierz polecenie **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="23d74-471">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="23d74-472">Nadaj klasie nazwę *TodoItem* i wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="23d74-472">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="23d74-473">Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="23d74-473">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="23d74-474">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="23d74-474">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="23d74-475">Dodaj folder o nazwie *models*.</span><span class="sxs-lookup"><span data-stu-id="23d74-475">Add a folder named *Models*.</span></span>

* <span data-ttu-id="23d74-476">Dodaj `TodoItem` klasę do folderu *models* o następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="23d74-476">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="23d74-477">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="23d74-477">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="23d74-478">Kliknij prawym przyciskiem myszy projekt.</span><span class="sxs-lookup"><span data-stu-id="23d74-478">Right-click the project.</span></span> <span data-ttu-id="23d74-479">Wybierz pozycję **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="23d74-479">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="23d74-480">Nazwij *modele*folderów.</span><span class="sxs-lookup"><span data-stu-id="23d74-480">Name the folder *Models*.</span></span>

  ![Nowy folder](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="23d74-482">Kliknij prawym przyciskiem myszy folder *modele* , a następnie wybierz pozycję **Dodaj** > **nowy plik** > **ogólna** > **pusta Klasa**.</span><span class="sxs-lookup"><span data-stu-id="23d74-482">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="23d74-483">Nazwij klasę *TodoItem*, a następnie kliknij pozycję **New (nowy**).</span><span class="sxs-lookup"><span data-stu-id="23d74-483">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="23d74-484">Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="23d74-484">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="23d74-485">`Id`Właściwość działa jako unikatowy klucz w relacyjnej bazie danych.</span><span class="sxs-lookup"><span data-stu-id="23d74-485">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="23d74-486">Klasy modelu mogą przejść do dowolnego miejsca w projekcie, ale folder *modele* jest używany przez Konwencję.</span><span class="sxs-lookup"><span data-stu-id="23d74-486">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="23d74-487">Dodawanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="23d74-487">Add a database context</span></span>

<span data-ttu-id="23d74-488">*Kontekst bazy danych* jest główną klasą, która koordynuje Entity Framework funkcji dla modelu danych.</span><span class="sxs-lookup"><span data-stu-id="23d74-488">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="23d74-489">Ta klasa jest tworzona przez wyprowadzanie z `Microsoft.EntityFrameworkCore.DbContext` klasy.</span><span class="sxs-lookup"><span data-stu-id="23d74-489">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="23d74-490">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="23d74-490">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="23d74-491">Kliknij prawym przyciskiem myszy folder *modele* i wybierz polecenie **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="23d74-491">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="23d74-492">Nadaj klasie nazwę *TodoContext* i kliknij przycisk **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="23d74-492">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="23d74-493">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="23d74-493">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="23d74-494">Dodaj `TodoContext` klasę do folderu *models* .</span><span class="sxs-lookup"><span data-stu-id="23d74-494">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="23d74-495">Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="23d74-495">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="23d74-496">Rejestrowanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="23d74-496">Register the database context</span></span>

<span data-ttu-id="23d74-497">W ASP.NET Core usługi, takie jak kontekst bazy danych, muszą być zarejestrowane z kontenerem [iniekcji zależności (di)](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="23d74-497">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="23d74-498">Kontener udostępnia usługę kontrolerom.</span><span class="sxs-lookup"><span data-stu-id="23d74-498">The container provides the service to controllers.</span></span>

<span data-ttu-id="23d74-499">Zaktualizuj *Startup.cs* o następujący wyróżniony kod:</span><span class="sxs-lookup"><span data-stu-id="23d74-499">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="23d74-500">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="23d74-500">The preceding code:</span></span>

* <span data-ttu-id="23d74-501">Usuwa nieużywane `using` deklaracje.</span><span class="sxs-lookup"><span data-stu-id="23d74-501">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="23d74-502">Dodaje kontekst bazy danych do kontenera DI.</span><span class="sxs-lookup"><span data-stu-id="23d74-502">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="23d74-503">Określa, że kontekst bazy danych będzie używać bazy danych w pamięci.</span><span class="sxs-lookup"><span data-stu-id="23d74-503">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="23d74-504">Dodawanie kontrolera</span><span class="sxs-lookup"><span data-stu-id="23d74-504">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="23d74-505">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="23d74-505">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="23d74-506">Kliknij prawym przyciskiem myszy folder *controllers* .</span><span class="sxs-lookup"><span data-stu-id="23d74-506">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="23d74-507">Wybierz pozycję **Dodaj** > **nowy element**.</span><span class="sxs-lookup"><span data-stu-id="23d74-507">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="23d74-508">W oknie dialogowym **Dodaj nowy element** wybierz szablon **Klasa kontrolera interfejsu API** .</span><span class="sxs-lookup"><span data-stu-id="23d74-508">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="23d74-509">Nadaj klasie nazwę *TodoController*i wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="23d74-509">Name the class *TodoController*, and select **Add**.</span></span>

  ![Okno dialogowe Dodawanie nowego elementu z kontrolerem w polu wyszukiwania i wybranym kontrolerem interfejsu API sieci Web](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="23d74-511">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="23d74-511">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="23d74-512">W folderze *controllers* Utwórz klasę o nazwie `TodoController` .</span><span class="sxs-lookup"><span data-stu-id="23d74-512">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="23d74-513">Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="23d74-513">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="23d74-514">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="23d74-514">The preceding code:</span></span>

* <span data-ttu-id="23d74-515">Definiuje klasę kontrolera interfejsu API bez metod.</span><span class="sxs-lookup"><span data-stu-id="23d74-515">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="23d74-516">Oznacza klasę [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) atrybutem.</span><span class="sxs-lookup"><span data-stu-id="23d74-516">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="23d74-517">Ten atrybut wskazuje, że kontroler odpowiada na żądania interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="23d74-517">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="23d74-518">Aby uzyskać informacje o określonych zachowaniach, które włącza atrybut, zobacz <xref:web-api/index> .</span><span class="sxs-lookup"><span data-stu-id="23d74-518">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="23d74-519">Używa funkcji DI do iniekcji kontekstu bazy danych ( `TodoContext` ) do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="23d74-519">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="23d74-520">Kontekst bazy danych jest używany w każdej z metod [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) w kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="23d74-520">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="23d74-521">Dodaje element o nazwie `Item1` do bazy danych, jeśli baza danych jest pusta.</span><span class="sxs-lookup"><span data-stu-id="23d74-521">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="23d74-522">Ten kod znajduje się w konstruktorze, więc jest uruchamiany za każdym razem, gdy istnieje nowe żądanie HTTP.</span><span class="sxs-lookup"><span data-stu-id="23d74-522">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="23d74-523">Jeśli usuniesz wszystkie elementy, Konstruktor zostanie ponownie utworzony `Item1` przy następnym wywołaniu metody interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="23d74-523">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="23d74-524">Dlatego może wyglądać podobnie jak usunięcie nie zadziałało, gdy rzeczywiście zadziałało.</span><span class="sxs-lookup"><span data-stu-id="23d74-524">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="23d74-525">Dodawanie metod get</span><span class="sxs-lookup"><span data-stu-id="23d74-525">Add Get methods</span></span>

<span data-ttu-id="23d74-526">Aby udostępnić interfejs API, który pobiera elementy do wykonania, Dodaj następujące metody do `TodoController` klasy:</span><span class="sxs-lookup"><span data-stu-id="23d74-526">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="23d74-527">Te metody implementują dwa punkty końcowe GET:</span><span class="sxs-lookup"><span data-stu-id="23d74-527">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="23d74-528">Zatrzymaj aplikację, jeśli jest nadal uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="23d74-528">Stop the app if it's still running.</span></span> <span data-ttu-id="23d74-529">Następnie uruchom ją ponownie, aby uwzględnić najnowsze zmiany.</span><span class="sxs-lookup"><span data-stu-id="23d74-529">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="23d74-530">Przetestuj aplikację, wywołując dwa punkty końcowe z przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="23d74-530">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="23d74-531">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="23d74-531">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="23d74-532">Następująca odpowiedź HTTP jest generowana przez wywołanie `GetTodoItems` :</span><span class="sxs-lookup"><span data-stu-id="23d74-532">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="23d74-533">Ścieżki routingu i adresów URL</span><span class="sxs-lookup"><span data-stu-id="23d74-533">Routing and URL paths</span></span>

<span data-ttu-id="23d74-534">Ten [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) atrybut oznacza metodę, która reaguje na żądanie HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="23d74-534">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="23d74-535">Ścieżka adresu URL dla każdej metody jest zbudowana w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="23d74-535">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="23d74-536">Zacznij od ciągu szablonu w `Route` atrybucie kontrolera:</span><span class="sxs-lookup"><span data-stu-id="23d74-536">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="23d74-537">Zastąp `[controller]` nazwą kontrolera, którą Konwencją jest nazwa klasy kontrolera minus sufiks "Controller".</span><span class="sxs-lookup"><span data-stu-id="23d74-537">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="23d74-538">W przypadku tego przykładu nazwa klasy kontrolera to kontroler do **zrobienia**, więc nazwa kontrolera to "do zrobienia".</span><span class="sxs-lookup"><span data-stu-id="23d74-538">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="23d74-539">W ASP.NET Core [routingu](xref:mvc/controllers/routing) jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="23d74-539">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="23d74-540">Jeśli `[HttpGet]` atrybut ma szablon trasy (na przykład `[HttpGet("products")]` ), Dodaj go do ścieżki.</span><span class="sxs-lookup"><span data-stu-id="23d74-540">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="23d74-541">Ten przykład nie używa szablonu.</span><span class="sxs-lookup"><span data-stu-id="23d74-541">This sample doesn't use a template.</span></span> <span data-ttu-id="23d74-542">Aby uzyskać więcej informacji, zobacz temat [Routing atrybutów z atrybutami http [Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="23d74-542">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="23d74-543">W poniższej `GetTodoItem` metodzie `"{id}"` jest zmienną zastępczą dla unikatowego identyfikatora elementu do wykonania.</span><span class="sxs-lookup"><span data-stu-id="23d74-543">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="23d74-544">Gdy `GetTodoItem` jest wywoływana, wartość `"{id}"` w adresie URL jest podawana do metody w jej `id` parametrze.</span><span class="sxs-lookup"><span data-stu-id="23d74-544">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="23d74-545">Wartości zwracane</span><span class="sxs-lookup"><span data-stu-id="23d74-545">Return values</span></span>

<span data-ttu-id="23d74-546">Zwracany typ `GetTodoItems` `GetTodoItem` metod i jest [ \<T> typem ActionResult](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="23d74-546">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="23d74-547">ASP.NET Core automatycznie serializować obiektu do [formatu JSON](https://www.json.org/) i zapisuje kod JSON w treści komunikatu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="23d74-547">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="23d74-548">Kod odpowiedzi dla tego typu zwracanego to 200, przy założeniu, że nie istnieją Nieobsłużone wyjątki.</span><span class="sxs-lookup"><span data-stu-id="23d74-548">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="23d74-549">Nieobsłużone wyjątki są tłumaczone na błędy 5xx.</span><span class="sxs-lookup"><span data-stu-id="23d74-549">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="23d74-550">`ActionResult`typy zwracane mogą reprezentować szeroką gamę kodów stanu HTTP.</span><span class="sxs-lookup"><span data-stu-id="23d74-550">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="23d74-551">Na przykład `GetTodoItem` może zwracać dwie różne wartości stanu:</span><span class="sxs-lookup"><span data-stu-id="23d74-551">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="23d74-552">Jeśli żaden element nie jest zgodny z żądanym IDENTYFIKATORem, metoda zwraca 404 kod błędu [NOTFOUND](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) .</span><span class="sxs-lookup"><span data-stu-id="23d74-552">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="23d74-553">W przeciwnym razie metoda zwraca 200 z treścią odpowiedzi JSON.</span><span class="sxs-lookup"><span data-stu-id="23d74-553">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="23d74-554">Zwracanie `item` wyników w odpowiedzi HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="23d74-554">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="23d74-555">Testowanie metody GetTodoItems</span><span class="sxs-lookup"><span data-stu-id="23d74-555">Test the GetTodoItems method</span></span>

<span data-ttu-id="23d74-556">Ten samouczek używa programu do testowania interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="23d74-556">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="23d74-557">Zainstaluj program [Poster](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="23d74-557">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="23d74-558">Uruchom aplikację internetową.</span><span class="sxs-lookup"><span data-stu-id="23d74-558">Start the web app.</span></span>
* <span data-ttu-id="23d74-559">Uruchom wpis.</span><span class="sxs-lookup"><span data-stu-id="23d74-559">Start Postman.</span></span>
* <span data-ttu-id="23d74-560">Wyłącz **weryfikację certyfikatu SSL**.</span><span class="sxs-lookup"><span data-stu-id="23d74-560">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="23d74-561">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="23d74-561">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="23d74-562">W **File** obszarze > **Ustawienia** pliku (karta**Ogólne** ) Wyłącz **weryfikację certyfikatu SSL**.</span><span class="sxs-lookup"><span data-stu-id="23d74-562">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="23d74-563">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="23d74-563">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="23d74-564">Z poziomu preferencji programu **Poster**  >  **Preferences** (karta**Ogólne** ) Wyłącz **weryfikację certyfikatu SSL**.</span><span class="sxs-lookup"><span data-stu-id="23d74-564">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="23d74-565">Alternatywnie wybierz klucz i wybierz pozycję **Ustawienia**, a następnie wyłącz weryfikację certyfikatu SSL.</span><span class="sxs-lookup"><span data-stu-id="23d74-565">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="23d74-566">Po przetestowaniu kontrolera ponownie Włącz weryfikację certyfikatu SSL.</span><span class="sxs-lookup"><span data-stu-id="23d74-566">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="23d74-567">Utwórz nowe żądanie.</span><span class="sxs-lookup"><span data-stu-id="23d74-567">Create a new request.</span></span>
  * <span data-ttu-id="23d74-568">Ustaw metodę HTTP, aby **uzyskać**.</span><span class="sxs-lookup"><span data-stu-id="23d74-568">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="23d74-569">Ustaw adres URL żądania `https://localhost:<port>/api/todo` .</span><span class="sxs-lookup"><span data-stu-id="23d74-569">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="23d74-570">Na przykład `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="23d74-570">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="23d74-571">Ustaw **dwa widoki okienka** w programie Poster.</span><span class="sxs-lookup"><span data-stu-id="23d74-571">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="23d74-572">Wybierz pozycję **Send** (Wyślij).</span><span class="sxs-lookup"><span data-stu-id="23d74-572">Select **Send**.</span></span>

![Ogłoś przy użyciu żądania GET](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="23d74-574">Dodawanie metody Create</span><span class="sxs-lookup"><span data-stu-id="23d74-574">Add a Create method</span></span>

<span data-ttu-id="23d74-575">Dodaj następującą `PostTodoItem` metodę w obszarze *controllers/TodoController. cs*:</span><span class="sxs-lookup"><span data-stu-id="23d74-575">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="23d74-576">Poprzedni kod jest metodą POST protokołu HTTP, jak wskazano w [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) atrybucie.</span><span class="sxs-lookup"><span data-stu-id="23d74-576">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="23d74-577">Metoda pobiera wartość elementu do wykonania z treści żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="23d74-577">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="23d74-578">`CreatedAtAction`Metoda:</span><span class="sxs-lookup"><span data-stu-id="23d74-578">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="23d74-579">Zwraca kod stanu HTTP 201, jeśli powodzenie.</span><span class="sxs-lookup"><span data-stu-id="23d74-579">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="23d74-580">HTTP 201 to standardowa odpowiedź dla metody POST protokołu HTTP, która tworzy nowy zasób na serwerze.</span><span class="sxs-lookup"><span data-stu-id="23d74-580">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="23d74-581">Dodaje `Location` nagłówek do odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="23d74-581">Adds a `Location` header to the response.</span></span> <span data-ttu-id="23d74-582">`Location`Nagłówek określa identyfikator URI nowo utworzonego elementu do wykonania.</span><span class="sxs-lookup"><span data-stu-id="23d74-582">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="23d74-583">Aby uzyskać więcej informacji, zobacz [10.2.2 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="23d74-583">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="23d74-584">Odwołuje się do `GetTodoItem` akcji tworzenia `Location` identyfikatora URI nagłówka.</span><span class="sxs-lookup"><span data-stu-id="23d74-584">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="23d74-585">`nameof`Słowo kluczowe języka C# służy do zapobiegania twardemu kodowaniu nazwy akcji w `CreatedAtAction` wywołaniu.</span><span class="sxs-lookup"><span data-stu-id="23d74-585">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="23d74-586">Testowanie metody PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="23d74-586">Test the PostTodoItem method</span></span>

* <span data-ttu-id="23d74-587">Skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="23d74-587">Build the project.</span></span>
* <span data-ttu-id="23d74-588">W programie Poster ustaw metodę HTTP na `POST` .</span><span class="sxs-lookup"><span data-stu-id="23d74-588">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="23d74-589">Wybierz kartę **Treść**.</span><span class="sxs-lookup"><span data-stu-id="23d74-589">Select the **Body** tab.</span></span>
* <span data-ttu-id="23d74-590">Wybierz przycisk radiowy **RAW** .</span><span class="sxs-lookup"><span data-stu-id="23d74-590">Select the **raw** radio button.</span></span>
* <span data-ttu-id="23d74-591">Ustaw typ na **JSON (Application/JSON)**.</span><span class="sxs-lookup"><span data-stu-id="23d74-591">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="23d74-592">W treści żądania wprowadź kod JSON dla elementu do wykonania:</span><span class="sxs-lookup"><span data-stu-id="23d74-592">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="23d74-593">Wybierz pozycję **Send** (Wyślij).</span><span class="sxs-lookup"><span data-stu-id="23d74-593">Select **Send**.</span></span>

  ![Ogłoś przy użyciu żądania Create](first-web-api/_static/create.png)

  <span data-ttu-id="23d74-595">Jeśli wystąpi błąd 405 metody niedozwolonej, jest to prawdopodobnie wynik niekompilowania projektu po dodaniu `PostTodoItem` metody.</span><span class="sxs-lookup"><span data-stu-id="23d74-595">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="23d74-596">Testowanie identyfikatora URI nagłówka lokalizacji</span><span class="sxs-lookup"><span data-stu-id="23d74-596">Test the location header URI</span></span>

* <span data-ttu-id="23d74-597">Wybierz kartę **nagłówki** w okienku **odpowiedź** .</span><span class="sxs-lookup"><span data-stu-id="23d74-597">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="23d74-598">Skopiuj wartość nagłówka **lokalizacji** :</span><span class="sxs-lookup"><span data-stu-id="23d74-598">Copy the **Location** header value:</span></span>

  ![Karta nagłówki w konsoli programu Poster](first-web-api/_static/pmc2.png)

* <span data-ttu-id="23d74-600">Ustaw metodę, aby uzyskać.</span><span class="sxs-lookup"><span data-stu-id="23d74-600">Set the method to GET.</span></span>
* <span data-ttu-id="23d74-601">Wklej URI (na przykład `https://localhost:5001/api/Todo/2` ).</span><span class="sxs-lookup"><span data-stu-id="23d74-601">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="23d74-602">Wybierz pozycję **Send** (Wyślij).</span><span class="sxs-lookup"><span data-stu-id="23d74-602">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="23d74-603">Dodawanie metody PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="23d74-603">Add a PutTodoItem method</span></span>

<span data-ttu-id="23d74-604">Dodaj następującą `PutTodoItem` metodę:</span><span class="sxs-lookup"><span data-stu-id="23d74-604">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="23d74-605">`PutTodoItem`jest podobny do `PostTodoItem` , z tą różnicą, że używa protokołu HTTP Put.</span><span class="sxs-lookup"><span data-stu-id="23d74-605">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="23d74-606">Odpowiedź to [204 (brak zawartości)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="23d74-606">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="23d74-607">Zgodnie ze specyfikacją protokołu HTTP żądanie PUT wymaga, aby klient wysłał całą zaktualizowaną jednostkę, a nie tylko te zmiany.</span><span class="sxs-lookup"><span data-stu-id="23d74-607">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="23d74-608">Aby zapewnić obsługę częściowych aktualizacji, użyj [poprawki http](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="23d74-608">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="23d74-609">Jeśli wystąpi błąd podczas wywoływania `PutTodoItem` , wywołaj, `GET` Aby upewnić się, że w bazie danych znajduje się element.</span><span class="sxs-lookup"><span data-stu-id="23d74-609">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="23d74-610">Testowanie metody PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="23d74-610">Test the PutTodoItem method</span></span>

<span data-ttu-id="23d74-611">Ten przykład korzysta z bazy danych w pamięci, która musi zostać zainicjowana za każdym razem, gdy aplikacja zostanie uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="23d74-611">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="23d74-612">Przed wykonaniem wywołania PUT musi istnieć element w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="23d74-612">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="23d74-613">Wywołaj polecenie GET, aby upewnić się, że w bazie danych znajduje się element, przed wykonaniem wywołania PUT.</span><span class="sxs-lookup"><span data-stu-id="23d74-613">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="23d74-614">Zaktualizuj element do wykonania o identyfikatorze 1 i ustaw jego nazwę na "Źródło danych":</span><span class="sxs-lookup"><span data-stu-id="23d74-614">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="23d74-615">Na poniższej ilustracji przedstawiono aktualizację programu Poster:</span><span class="sxs-lookup"><span data-stu-id="23d74-615">The following image shows the Postman update:</span></span>

![Konsola programu Poster pokazująca odpowiedź 204 (brak zawartości)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="23d74-617">Dodawanie metody DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="23d74-617">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="23d74-618">Dodaj następującą `DeleteTodoItem` metodę:</span><span class="sxs-lookup"><span data-stu-id="23d74-618">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="23d74-619">`DeleteTodoItem`Odpowiedź to [204 (brak zawartości)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="23d74-619">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="23d74-620">Testowanie metody DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="23d74-620">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="23d74-621">Użyj programu Poster, aby usunąć element do wykonania:</span><span class="sxs-lookup"><span data-stu-id="23d74-621">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="23d74-622">Ustaw metodę na `DELETE` .</span><span class="sxs-lookup"><span data-stu-id="23d74-622">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="23d74-623">Ustaw identyfikator URI obiektu do usunięcia (na przykład `https://localhost:5001/api/todo/1` ).</span><span class="sxs-lookup"><span data-stu-id="23d74-623">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="23d74-624">Wybierz pozycję **Send** (Wyślij).</span><span class="sxs-lookup"><span data-stu-id="23d74-624">Select **Send**.</span></span>

<span data-ttu-id="23d74-625">Przykładowa aplikacja umożliwia usunięcie wszystkich elementów.</span><span class="sxs-lookup"><span data-stu-id="23d74-625">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="23d74-626">Jednak po usunięciu ostatniego elementu jest on tworzony przez konstruktora klasy modelu przy następnym wywołaniu interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="23d74-626">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="23d74-627">Wywoływanie interfejsu API sieci Web przy użyciu języka JavaScript</span><span class="sxs-lookup"><span data-stu-id="23d74-627">Call the web API with JavaScript</span></span>

<span data-ttu-id="23d74-628">W tej sekcji zostanie dodana strona HTML, która używa języka JavaScript do wywoływania internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="23d74-628">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="23d74-629">jQuery inicjuje żądanie.</span><span class="sxs-lookup"><span data-stu-id="23d74-629">jQuery initiates the request.</span></span> <span data-ttu-id="23d74-630">Język JavaScript aktualizuje stronę ze szczegółowymi informacjami z odpowiedzi internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="23d74-630">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="23d74-631">Skonfiguruj aplikację do [obsługi plików statycznych](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) i [Włącz domyślne mapowanie plików](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) , aktualizując *Startup.cs* z następującym wyróżnionym kodem:</span><span class="sxs-lookup"><span data-stu-id="23d74-631">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="23d74-632">Utwórz folder *wwwroot* w katalogu projektu.</span><span class="sxs-lookup"><span data-stu-id="23d74-632">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="23d74-633">Dodaj plik HTML o nazwie *index.html* do katalogu *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="23d74-633">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="23d74-634">Zastąp jego zawartość następującym znacznikiem:</span><span class="sxs-lookup"><span data-stu-id="23d74-634">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="23d74-635">Dodaj plik języka JavaScript o nazwie *site.js* do katalogu *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="23d74-635">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="23d74-636">Zastąp zawartość poniższym kodem:</span><span class="sxs-lookup"><span data-stu-id="23d74-636">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="23d74-637">Zmiana ustawień uruchamiania projektu ASP.NET Core może być wymagana do lokalnego przetestowania strony HTML:</span><span class="sxs-lookup"><span data-stu-id="23d74-637">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="23d74-638">Otwórz *Properties\launchSettings.jsna*.</span><span class="sxs-lookup"><span data-stu-id="23d74-638">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="23d74-639">Usuń `launchUrl` Właściwość, aby wymusić otwieranie przez aplikację w *index.html* &mdash; domyślnego pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="23d74-639">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="23d74-640">Ten przykład wywołuje wszystkie metody CRUD internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="23d74-640">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="23d74-641">Poniżej znajdują się wyjaśnienia wywołań interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="23d74-641">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="23d74-642">Pobierz listę elementów do wykonania</span><span class="sxs-lookup"><span data-stu-id="23d74-642">Get a list of to-do items</span></span>

<span data-ttu-id="23d74-643">jQuery wysyła żądanie HTTP GET do internetowego interfejsu API, który zwraca kod JSON reprezentujący tablicę elementów do wykonania.</span><span class="sxs-lookup"><span data-stu-id="23d74-643">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="23d74-644">`success`Funkcja wywołania zwrotnego jest wywoływana, jeśli żądanie zakończy się pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="23d74-644">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="23d74-645">W wywołaniu zwrotnym model DOM jest aktualizowany przy użyciu informacji o tym do wykonania.</span><span class="sxs-lookup"><span data-stu-id="23d74-645">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="23d74-646">Dodaj element do wykonania</span><span class="sxs-lookup"><span data-stu-id="23d74-646">Add a to-do item</span></span>

<span data-ttu-id="23d74-647">jQuery wysyła żądanie HTTP POST z elementem do wykonania w treści żądania.</span><span class="sxs-lookup"><span data-stu-id="23d74-647">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="23d74-648">`accepts`Opcje i `contentType` są ustawione na, aby `application/json` określić typ nośnika odbierany i wysyłany.</span><span class="sxs-lookup"><span data-stu-id="23d74-648">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="23d74-649">Element do wykonania jest konwertowany na format JSON przy użyciu [formatu JSON. stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="23d74-649">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="23d74-650">Gdy interfejs API zwraca kod stanu pomyślnego, `getData` Funkcja jest wywoływana w celu zaktualizowania tabeli HTML.</span><span class="sxs-lookup"><span data-stu-id="23d74-650">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="23d74-651">Aktualizowanie elementu do wykonania</span><span class="sxs-lookup"><span data-stu-id="23d74-651">Update a to-do item</span></span>

<span data-ttu-id="23d74-652">Aktualizowanie elementu do wykonania jest podobne do dodawania jednego.</span><span class="sxs-lookup"><span data-stu-id="23d74-652">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="23d74-653">`url`Zmiany mające na celu dodanie unikatowego identyfikatora elementu i `type` is `PUT` .</span><span class="sxs-lookup"><span data-stu-id="23d74-653">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="23d74-654">Usuń element do wykonania</span><span class="sxs-lookup"><span data-stu-id="23d74-654">Delete a to-do item</span></span>

<span data-ttu-id="23d74-655">Usuwanie elementu do wykonania jest realizowane przez ustawienie `type` w WYWOŁANIU AJAX `DELETE` i określenie unikatowego identyfikatora elementu w adresie URL.</span><span class="sxs-lookup"><span data-stu-id="23d74-655">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="23d74-656">Dodawanie obsługi uwierzytelniania do internetowego interfejsu API</span><span class="sxs-lookup"><span data-stu-id="23d74-656">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a><span data-ttu-id="23d74-657">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="23d74-657">Additional resources</span></span>

<span data-ttu-id="23d74-658">[Wyświetl lub Pobierz przykładowy kod dla tego samouczka](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="23d74-658">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="23d74-659">Zobacz artykuł [jak pobrać](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="23d74-659">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="23d74-660">Więcej informacji można znaleźć w następujących zasobach:</span><span class="sxs-lookup"><span data-stu-id="23d74-660">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="23d74-661">Wersja tego samouczka usługi YouTube</span><span class="sxs-lookup"><span data-stu-id="23d74-661">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
