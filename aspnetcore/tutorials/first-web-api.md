---
title: 'Samouczek: Tworzenie internetowego interfejsu API za pomocą ASP.NET Core'
author: rick-anderson
description: Dowiedz się, jak utworzyć internetowy interfejs API za pomocą ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 2/25/2020
uid: tutorials/first-web-api
ms.openlocfilehash: 4e205c737f606579590854b679e669cbdd0cd5ab
ms.sourcegitcommit: c19e388c83c981232e6f128d97440262adfe06e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2020
ms.locfileid: "82727794"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="7652f-103">Samouczek: Tworzenie internetowego interfejsu API za pomocą ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7652f-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="7652f-104">Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirka Larkin](https://twitter.com/serpent5)i [Jan Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="7652f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="7652f-105">Ten samouczek uczy się podstaw tworzenia interfejsu API sieci Web za pomocą ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7652f-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7652f-106">Ten samouczek zawiera informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="7652f-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="7652f-107">Utwórz projekt interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="7652f-107">Create a web API project.</span></span>
> * <span data-ttu-id="7652f-108">Dodaj klasę modelu i kontekst bazy danych.</span><span class="sxs-lookup"><span data-stu-id="7652f-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="7652f-109">Tworzy szkielet kontrolera z metodami CRUD.</span><span class="sxs-lookup"><span data-stu-id="7652f-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="7652f-110">Skonfiguruj Routing, ścieżki URL i wartości zwracane.</span><span class="sxs-lookup"><span data-stu-id="7652f-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="7652f-111">Wywołaj interfejs API sieci Web za pomocą programu Poster.</span><span class="sxs-lookup"><span data-stu-id="7652f-111">Call the web API with Postman.</span></span>

<span data-ttu-id="7652f-112">Na końcu znajduje się internetowy interfejs API, który może zarządzać elementami do wykonania przechowywanymi w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="7652f-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="7652f-113">Omówienie</span><span class="sxs-lookup"><span data-stu-id="7652f-113">Overview</span></span>

<span data-ttu-id="7652f-114">Ten samouczek tworzy następujący interfejs API:</span><span class="sxs-lookup"><span data-stu-id="7652f-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="7652f-115">Interfejs API</span><span class="sxs-lookup"><span data-stu-id="7652f-115">API</span></span> | <span data-ttu-id="7652f-116">Opis</span><span class="sxs-lookup"><span data-stu-id="7652f-116">Description</span></span> | <span data-ttu-id="7652f-117">Treść żądania</span><span class="sxs-lookup"><span data-stu-id="7652f-117">Request body</span></span> | <span data-ttu-id="7652f-118">Treść odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="7652f-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="7652f-119">Pobierz wszystkie elementy do wykonania</span><span class="sxs-lookup"><span data-stu-id="7652f-119">Get all to-do items</span></span> | <span data-ttu-id="7652f-120">Brak</span><span class="sxs-lookup"><span data-stu-id="7652f-120">None</span></span> | <span data-ttu-id="7652f-121">Tablica elementów do wykonania</span><span class="sxs-lookup"><span data-stu-id="7652f-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="7652f-122">Pobieranie elementu według identyfikatora</span><span class="sxs-lookup"><span data-stu-id="7652f-122">Get an item by ID</span></span> | <span data-ttu-id="7652f-123">Brak</span><span class="sxs-lookup"><span data-stu-id="7652f-123">None</span></span> | <span data-ttu-id="7652f-124">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="7652f-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="7652f-125">Dodaj nowy element</span><span class="sxs-lookup"><span data-stu-id="7652f-125">Add a new item</span></span> | <span data-ttu-id="7652f-126">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="7652f-126">To-do item</span></span> | <span data-ttu-id="7652f-127">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="7652f-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="7652f-128">Aktualizowanie istniejącego elementu&nbsp;</span><span class="sxs-lookup"><span data-stu-id="7652f-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="7652f-129">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="7652f-129">To-do item</span></span> | <span data-ttu-id="7652f-130">Brak</span><span class="sxs-lookup"><span data-stu-id="7652f-130">None</span></span> |
|<span data-ttu-id="7652f-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="7652f-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="7652f-132">Usuń element &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="7652f-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="7652f-133">Brak</span><span class="sxs-lookup"><span data-stu-id="7652f-133">None</span></span> | <span data-ttu-id="7652f-134">Brak</span><span class="sxs-lookup"><span data-stu-id="7652f-134">None</span></span>|

<span data-ttu-id="7652f-135">Na poniższym diagramie przedstawiono projekt aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7652f-135">The following diagram shows the design of the app.</span></span>

![Klient jest reprezentowany przez pole po lewej stronie.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="7652f-141">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="7652f-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7652f-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7652f-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="7652f-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7652f-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7652f-144">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="7652f-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="7652f-145">Tworzenie projektu sieci Web</span><span class="sxs-lookup"><span data-stu-id="7652f-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7652f-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7652f-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7652f-147">Z menu **plik** wybierz pozycję **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="7652f-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="7652f-148">Wybierz szablon **aplikacja sieci Web ASP.NET Core** a następnie kliknij przycisk **dalej**.</span><span class="sxs-lookup"><span data-stu-id="7652f-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="7652f-149">Nazwij projekt *TodoApi* i kliknij pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="7652f-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="7652f-150">W oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** upewnij się, że wybrano opcję **.net Core** i **ASP.NET Core 3,1** .</span><span class="sxs-lookup"><span data-stu-id="7652f-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="7652f-151">Wybierz szablon **interfejsu API** i kliknij przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="7652f-151">Select the **API** template and click **Create**.</span></span>

![Okno dialogowe programu VS New Project](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="7652f-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7652f-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7652f-154">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="7652f-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="7652f-155">Zmień katalog (`cd`) do folderu, który będzie zawierać folder projektu.</span><span class="sxs-lookup"><span data-stu-id="7652f-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="7652f-156">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="7652f-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="7652f-157">Gdy zostanie wyświetlone okno dialogowe z pytaniem, czy chcesz dodać wymagane zasoby do projektu, wybierz opcję **tak**.</span><span class="sxs-lookup"><span data-stu-id="7652f-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="7652f-158">Poprzednie polecenia:</span><span class="sxs-lookup"><span data-stu-id="7652f-158">The preceding commands:</span></span>

  * <span data-ttu-id="7652f-159">Tworzy nowy projekt internetowego interfejsu API i otwiera go w Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="7652f-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="7652f-160">Dodaje pakiety NuGet, które są wymagane w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="7652f-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7652f-161">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="7652f-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="7652f-162">Wybierz pozycję **plik** > **nowe rozwiązanie**.</span><span class="sxs-lookup"><span data-stu-id="7652f-162">Select **File** > **New Solution**.</span></span>

  ![macOS nowe rozwiązanie](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="7652f-164">Wybierz pozycję **interfejs API** > **App** > > **Next**aplikacji **.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="7652f-164">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![okno dialogowe nowego projektu macOS](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="7652f-166">W oknie dialogowym **Konfigurowanie nowego interfejsu API sieci Web ASP.NET Core** wybierz pozycję **docelowa platforma** \**.NET Core 3,1*.</span><span class="sxs-lookup"><span data-stu-id="7652f-166">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.1*.</span></span>

* <span data-ttu-id="7652f-167">Wprowadź *TodoApi* jako **nazwę projektu** , a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="7652f-167">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![okno dialogowe konfiguracji](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="7652f-169">Otwórz Terminal poleceń w folderze projektu i uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="7652f-169">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="7652f-170">Testowanie interfejsu API</span><span class="sxs-lookup"><span data-stu-id="7652f-170">Test the API</span></span>

<span data-ttu-id="7652f-171">Szablon projektu tworzy `WeatherForecast` interfejs API.</span><span class="sxs-lookup"><span data-stu-id="7652f-171">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="7652f-172">Wywołaj `Get` metodę z przeglądarki, aby przetestować aplikację.</span><span class="sxs-lookup"><span data-stu-id="7652f-172">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7652f-173">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7652f-173">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7652f-174">Naciśnij klawisze CTRL + F5, aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="7652f-174">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="7652f-175">Program Visual Studio uruchamia przeglądarkę i przechodzi do `https://localhost:<port>/WeatherForecast`lokalizacji, gdzie `<port>` jest losowo wybierany numer portu.</span><span class="sxs-lookup"><span data-stu-id="7652f-175">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="7652f-176">Jeśli zostanie wyświetlone okno dialogowe z pytaniem, czy należy zaufać certyfikatowi IIS Express, wybierz pozycję **tak**.</span><span class="sxs-lookup"><span data-stu-id="7652f-176">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="7652f-177">W wyświetlonym oknie dialogowym **ostrzeżenia o zabezpieczeniach** wybierz pozycję **tak**.</span><span class="sxs-lookup"><span data-stu-id="7652f-177">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7652f-178">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7652f-178">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7652f-179">Naciśnij klawisze CTRL + F5, aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="7652f-179">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="7652f-180">W przeglądarce przejdź do następującego adresu URL: `https://localhost:5001/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="7652f-180">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7652f-181">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="7652f-181">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="7652f-182">Wybierz pozycję **Uruchom** > **Rozpocznij debugowanie** , aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="7652f-182">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="7652f-183">Visual Studio dla komputerów Mac uruchamia przeglądarkę i nawiguje do `https://localhost:<port>`, gdzie `<port>` jest losowo wybranym numerem portu.</span><span class="sxs-lookup"><span data-stu-id="7652f-183">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="7652f-184">Zwracany jest błąd HTTP 404 (nie znaleziono).</span><span class="sxs-lookup"><span data-stu-id="7652f-184">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="7652f-185">Dołącz `/WeatherForecast` do adresu URL (Zmień adres URL na `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="7652f-185">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="7652f-186">Zwracany jest kod JSON podobny do następującego:</span><span class="sxs-lookup"><span data-stu-id="7652f-186">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="7652f-187">Dodaj klasę modelu</span><span class="sxs-lookup"><span data-stu-id="7652f-187">Add a model class</span></span>

<span data-ttu-id="7652f-188">*Model* to zestaw klas, które reprezentują dane zarządzane przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="7652f-188">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="7652f-189">Model tej aplikacji jest pojedynczą `TodoItem` klasą.</span><span class="sxs-lookup"><span data-stu-id="7652f-189">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7652f-190">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7652f-190">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7652f-191">W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt.</span><span class="sxs-lookup"><span data-stu-id="7652f-191">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="7652f-192">Wybierz pozycję **Dodaj** > **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="7652f-192">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="7652f-193">Nazwij *modele*folderów.</span><span class="sxs-lookup"><span data-stu-id="7652f-193">Name the folder *Models*.</span></span>

* <span data-ttu-id="7652f-194">Kliknij prawym przyciskiem myszy folder *modele* i wybierz polecenie **Dodaj** > **klasę**.</span><span class="sxs-lookup"><span data-stu-id="7652f-194">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="7652f-195">Nadaj klasie nazwę *TodoItem* i wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="7652f-195">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="7652f-196">Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="7652f-196">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7652f-197">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7652f-197">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7652f-198">Dodaj folder o nazwie *models*.</span><span class="sxs-lookup"><span data-stu-id="7652f-198">Add a folder named *Models*.</span></span>

* <span data-ttu-id="7652f-199">Dodaj `TodoItem` klasę do folderu *models* o następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="7652f-199">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7652f-200">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="7652f-200">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="7652f-201">Kliknij prawym przyciskiem myszy projekt.</span><span class="sxs-lookup"><span data-stu-id="7652f-201">Right-click the project.</span></span> <span data-ttu-id="7652f-202">Wybierz pozycję **Dodaj** > **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="7652f-202">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="7652f-203">Nazwij *modele*folderów.</span><span class="sxs-lookup"><span data-stu-id="7652f-203">Name the folder *Models*.</span></span>

  ![Nowy folder](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="7652f-205">Kliknij prawym przyciskiem myszy folder *modele* , a następnie wybierz pozycję **Dodaj** > **nowy plik** > **ogólna** > **pusta Klasa**.</span><span class="sxs-lookup"><span data-stu-id="7652f-205">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="7652f-206">Nazwij klasę *TodoItem*, a następnie kliknij pozycję **New (nowy**).</span><span class="sxs-lookup"><span data-stu-id="7652f-206">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="7652f-207">Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="7652f-207">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="7652f-208">`Id` Właściwość działa jako unikatowy klucz w relacyjnej bazie danych.</span><span class="sxs-lookup"><span data-stu-id="7652f-208">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="7652f-209">Klasy modelu mogą przejść do dowolnego miejsca w projekcie, ale folder *modele* jest używany przez Konwencję.</span><span class="sxs-lookup"><span data-stu-id="7652f-209">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="7652f-210">Dodawanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="7652f-210">Add a database context</span></span>

<span data-ttu-id="7652f-211">*Kontekst bazy danych* jest główną klasą, która koordynuje Entity Framework funkcji dla modelu danych.</span><span class="sxs-lookup"><span data-stu-id="7652f-211">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="7652f-212">Ta klasa jest tworzona przez wyprowadzanie z `Microsoft.EntityFrameworkCore.DbContext` klasy.</span><span class="sxs-lookup"><span data-stu-id="7652f-212">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7652f-213">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7652f-213">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="7652f-214">Dodaj Microsoft. EntityFrameworkCore. SqlServer</span><span class="sxs-lookup"><span data-stu-id="7652f-214">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="7652f-215">W menu **Narzędzia** wybierz pozycję **menedżer pakietów NuGet > zarządzanie pakietami NuGet dla rozwiązania**.</span><span class="sxs-lookup"><span data-stu-id="7652f-215">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="7652f-216">Wybierz kartę **Przeglądaj** , a następnie w polu wyszukiwania wprowadź ciąg **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="7652f-216">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="7652f-217">W lewym okienku wybierz pozycję **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="7652f-217">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="7652f-218">Zaznacz pole wyboru **projekt** w prawym okienku, a następnie wybierz pozycję **Zainstaluj**.</span><span class="sxs-lookup"><span data-stu-id="7652f-218">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="7652f-219">Aby dodać pakiet `Microsoft.EntityFrameworkCore.InMemory` NuGet, użyj powyższych instrukcji.</span><span class="sxs-lookup"><span data-stu-id="7652f-219">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![Menedżer pakietów NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="7652f-221">Dodawanie kontekstu bazy danych TodoContext</span><span class="sxs-lookup"><span data-stu-id="7652f-221">Add the TodoContext database context</span></span>

* <span data-ttu-id="7652f-222">Kliknij prawym przyciskiem myszy folder *modele* i wybierz polecenie **Dodaj** > **klasę**.</span><span class="sxs-lookup"><span data-stu-id="7652f-222">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="7652f-223">Nadaj klasie nazwę *TodoContext* i kliknij przycisk **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="7652f-223">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7652f-224">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="7652f-224">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="7652f-225">Dodaj `TodoContext` klasę do folderu *models* .</span><span class="sxs-lookup"><span data-stu-id="7652f-225">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="7652f-226">Wprowadź następujący kod:</span><span class="sxs-lookup"><span data-stu-id="7652f-226">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="7652f-227">Rejestrowanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="7652f-227">Register the database context</span></span>

<span data-ttu-id="7652f-228">W ASP.NET Core usługi, takie jak kontekst bazy danych, muszą być zarejestrowane z kontenerem [iniekcji zależności (di)](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="7652f-228">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="7652f-229">Kontener udostępnia usługę kontrolerom.</span><span class="sxs-lookup"><span data-stu-id="7652f-229">The container provides the service to controllers.</span></span>

<span data-ttu-id="7652f-230">Zaktualizuj *Startup.cs* o następujący wyróżniony kod:</span><span class="sxs-lookup"><span data-stu-id="7652f-230">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="7652f-231">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="7652f-231">The preceding code:</span></span>

* <span data-ttu-id="7652f-232">Usuwa nieużywane `using` deklaracje.</span><span class="sxs-lookup"><span data-stu-id="7652f-232">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="7652f-233">Dodaje kontekst bazy danych do kontenera DI.</span><span class="sxs-lookup"><span data-stu-id="7652f-233">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="7652f-234">Określa, że kontekst bazy danych będzie używać bazy danych w pamięci.</span><span class="sxs-lookup"><span data-stu-id="7652f-234">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="7652f-235">Tworzenie szkieletu kontrolera</span><span class="sxs-lookup"><span data-stu-id="7652f-235">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7652f-236">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7652f-236">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7652f-237">Kliknij prawym przyciskiem myszy folder *controllers* .</span><span class="sxs-lookup"><span data-stu-id="7652f-237">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="7652f-238">Wybierz pozycję **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="7652f-238">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="7652f-239">Wybierz pozycję **kontroler interfejsu API z akcjami, używając Entity Framework**, a następnie wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="7652f-239">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="7652f-240">Na stronie **Dodawanie kontrolera interfejsu API z akcjami przy użyciu Entity Framework** dialogowego:</span><span class="sxs-lookup"><span data-stu-id="7652f-240">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="7652f-241">Wybierz pozycję **TodoItem (TodoApi. models)** w **klasie model**.</span><span class="sxs-lookup"><span data-stu-id="7652f-241">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="7652f-242">W **klasie kontekstu danych**wybierz pozycję **TodoContext (TodoApi. models)** .</span><span class="sxs-lookup"><span data-stu-id="7652f-242">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="7652f-243">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="7652f-243">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7652f-244">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="7652f-244">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="7652f-245">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="7652f-245">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="7652f-246">Poprzednie polecenia:</span><span class="sxs-lookup"><span data-stu-id="7652f-246">The preceding commands:</span></span>

* <span data-ttu-id="7652f-247">Dodaj pakiety NuGet wymagane do tworzenia szkieletów.</span><span class="sxs-lookup"><span data-stu-id="7652f-247">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="7652f-248">Instaluje aparat tworzenia szkieletu (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="7652f-248">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="7652f-249">Szkielety `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="7652f-249">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="7652f-250">Wygenerowany kod:</span><span class="sxs-lookup"><span data-stu-id="7652f-250">The generated code:</span></span>

* <span data-ttu-id="7652f-251">Oznacza klasę [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) atrybutem.</span><span class="sxs-lookup"><span data-stu-id="7652f-251">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="7652f-252">Ten atrybut wskazuje, że kontroler odpowiada na żądania interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="7652f-252">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="7652f-253">Aby uzyskać informacje o określonych zachowaniach, które włącza atrybut <xref:web-api/index>, zobacz.</span><span class="sxs-lookup"><span data-stu-id="7652f-253">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="7652f-254">Używa funkcji DI do iniekcji kontekstu bazy`TodoContext`danych () do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="7652f-254">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="7652f-255">Kontekst bazy danych jest używany w każdej z metod [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) w kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="7652f-255">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="7652f-256">Szablony ASP.NET Core dla:</span><span class="sxs-lookup"><span data-stu-id="7652f-256">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="7652f-257">Kontrolery z widokami `[action]` obejmują szablon trasy.</span><span class="sxs-lookup"><span data-stu-id="7652f-257">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="7652f-258">Kontrolery interfejsu API nie `[action]` należą do szablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="7652f-258">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="7652f-259">Gdy `[action]` token nie znajduje się w szablonie trasy, nazwa [akcji](xref:mvc/controllers/routing#action) jest wykluczona z trasy.</span><span class="sxs-lookup"><span data-stu-id="7652f-259">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="7652f-260">Oznacza to, że nazwa metody skojarzonej z akcją nie jest używana w zgodnej trasie.</span><span class="sxs-lookup"><span data-stu-id="7652f-260">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="7652f-261">Badanie metody PostTodoItem Create</span><span class="sxs-lookup"><span data-stu-id="7652f-261">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="7652f-262">Zastąp instrukcję return w, `PostTodoItem` aby użyć operatora [nameof](/dotnet/csharp/language-reference/operators/nameof) :</span><span class="sxs-lookup"><span data-stu-id="7652f-262">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="7652f-263">Poprzedni kod jest metodą POST protokołu HTTP, jak wskazano w [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) atrybucie.</span><span class="sxs-lookup"><span data-stu-id="7652f-263">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="7652f-264">Metoda pobiera wartość elementu do wykonania z treści żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="7652f-264">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="7652f-265"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> Metoda:</span><span class="sxs-lookup"><span data-stu-id="7652f-265">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="7652f-266">W razie powodzenia zwraca kod stanu HTTP 201.</span><span class="sxs-lookup"><span data-stu-id="7652f-266">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="7652f-267">HTTP 201 to standardowa odpowiedź dla metody POST protokołu HTTP, która tworzy nowy zasób na serwerze.</span><span class="sxs-lookup"><span data-stu-id="7652f-267">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="7652f-268">Dodaje nagłówek [lokalizacji](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) do odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="7652f-268">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="7652f-269">`Location` Nagłówek określa [Identyfikator URI](https://developer.mozilla.org/docs/Glossary/URI) nowo utworzonego elementu do wykonania.</span><span class="sxs-lookup"><span data-stu-id="7652f-269">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="7652f-270">Aby uzyskać więcej informacji, zobacz [10.2.2 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="7652f-270">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="7652f-271">Odwołuje `GetTodoItem` się do akcji tworzenia `Location` identyfikatora URI nagłówka.</span><span class="sxs-lookup"><span data-stu-id="7652f-271">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="7652f-272">Słowo kluczowe `nameof` języka C# służy do zapobiegania twardemu kodowaniu nazwy akcji w `CreatedAtAction` wywołaniu.</span><span class="sxs-lookup"><span data-stu-id="7652f-272">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="7652f-273">Zainstaluj program Poster</span><span class="sxs-lookup"><span data-stu-id="7652f-273">Install Postman</span></span>

<span data-ttu-id="7652f-274">Ten samouczek używa programu do testowania interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="7652f-274">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="7652f-275">Zainstaluj program [Poster](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="7652f-275">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="7652f-276">Uruchom aplikację internetową.</span><span class="sxs-lookup"><span data-stu-id="7652f-276">Start the web app.</span></span>
* <span data-ttu-id="7652f-277">Uruchom wpis.</span><span class="sxs-lookup"><span data-stu-id="7652f-277">Start Postman.</span></span>
* <span data-ttu-id="7652f-278">Wyłącz **weryfikację certyfikatu SSL**</span><span class="sxs-lookup"><span data-stu-id="7652f-278">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="7652f-279">W obszarze **Ustawienia** **pliku** > (karta**Ogólne** ) Wyłącz **weryfikację certyfikatu SSL**.</span><span class="sxs-lookup"><span data-stu-id="7652f-279">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="7652f-280">Po przetestowaniu kontrolera ponownie Włącz weryfikację certyfikatu SSL.</span><span class="sxs-lookup"><span data-stu-id="7652f-280">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="7652f-281">Test PostTodoItem za pomocą programu Poster</span><span class="sxs-lookup"><span data-stu-id="7652f-281">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="7652f-282">Utwórz nowe żądanie.</span><span class="sxs-lookup"><span data-stu-id="7652f-282">Create a new request.</span></span>
* <span data-ttu-id="7652f-283">Ustaw metodę HTTP na `POST`.</span><span class="sxs-lookup"><span data-stu-id="7652f-283">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="7652f-284">Wybierz kartę **Treść**.</span><span class="sxs-lookup"><span data-stu-id="7652f-284">Select the **Body** tab.</span></span>
* <span data-ttu-id="7652f-285">Wybierz przycisk radiowy **RAW** .</span><span class="sxs-lookup"><span data-stu-id="7652f-285">Select the **raw** radio button.</span></span>
* <span data-ttu-id="7652f-286">Ustaw typ na **JSON (Application/JSON)**.</span><span class="sxs-lookup"><span data-stu-id="7652f-286">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="7652f-287">W treści żądania wprowadź kod JSON dla elementu do wykonania:</span><span class="sxs-lookup"><span data-stu-id="7652f-287">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="7652f-288">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="7652f-288">Select **Send**.</span></span>

  ![Ogłoś przy użyciu żądania Create](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="7652f-290">Testowanie identyfikatora URI nagłówka lokalizacji</span><span class="sxs-lookup"><span data-stu-id="7652f-290">Test the location header URI</span></span>

* <span data-ttu-id="7652f-291">Wybierz kartę **nagłówki** w okienku **odpowiedź** .</span><span class="sxs-lookup"><span data-stu-id="7652f-291">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="7652f-292">Skopiuj wartość nagłówka **lokalizacji** :</span><span class="sxs-lookup"><span data-stu-id="7652f-292">Copy the **Location** header value:</span></span>

  ![Karta nagłówki w konsoli programu Poster](first-web-api/_static/3/create.png)

* <span data-ttu-id="7652f-294">Ustaw metodę, aby uzyskać.</span><span class="sxs-lookup"><span data-stu-id="7652f-294">Set the method to GET.</span></span>
* <span data-ttu-id="7652f-295">Wklej URI (na przykład `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="7652f-295">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="7652f-296">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="7652f-296">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="7652f-297">Badanie metod GET</span><span class="sxs-lookup"><span data-stu-id="7652f-297">Examine the GET methods</span></span>

<span data-ttu-id="7652f-298">Te metody implementują dwa punkty końcowe GET:</span><span class="sxs-lookup"><span data-stu-id="7652f-298">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="7652f-299">Przetestuj aplikację, wywołując dwa punkty końcowe z przeglądarki lub wpisu.</span><span class="sxs-lookup"><span data-stu-id="7652f-299">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="7652f-300">Przykład:</span><span class="sxs-lookup"><span data-stu-id="7652f-300">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="7652f-301">Odpowiedź podobna do poniższego jest generowana przez wywołanie `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="7652f-301">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="7652f-302">Test get przy użyciu programu Poster</span><span class="sxs-lookup"><span data-stu-id="7652f-302">Test Get with Postman</span></span>

* <span data-ttu-id="7652f-303">Utwórz nowe żądanie.</span><span class="sxs-lookup"><span data-stu-id="7652f-303">Create a new request.</span></span>
* <span data-ttu-id="7652f-304">Ustaw metodę HTTP, aby **uzyskać**.</span><span class="sxs-lookup"><span data-stu-id="7652f-304">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="7652f-305">Ustaw adres URL żądania `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="7652f-305">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="7652f-306">Na przykład `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="7652f-306">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="7652f-307">Ustaw **dwa widoki okienka** w programie Poster.</span><span class="sxs-lookup"><span data-stu-id="7652f-307">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="7652f-308">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="7652f-308">Select **Send**.</span></span>

<span data-ttu-id="7652f-309">Ta aplikacja używa bazy danych w pamięci.</span><span class="sxs-lookup"><span data-stu-id="7652f-309">This app uses an in-memory database.</span></span> <span data-ttu-id="7652f-310">Jeśli aplikacja zostanie zatrzymana i uruchomiona, poprzednie żądanie GET nie zwróci żadnych danych.</span><span class="sxs-lookup"><span data-stu-id="7652f-310">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="7652f-311">Jeśli nie zostaną zwrócone żadne dane, [Opublikuj](#post) dane w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7652f-311">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="7652f-312">Ścieżki routingu i adresów URL</span><span class="sxs-lookup"><span data-stu-id="7652f-312">Routing and URL paths</span></span>

<span data-ttu-id="7652f-313">Ten [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) atrybut oznacza metodę, która reaguje na żądanie HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="7652f-313">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="7652f-314">Ścieżka adresu URL dla każdej metody jest zbudowana w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="7652f-314">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="7652f-315">Zacznij od ciągu szablonu w `Route` atrybucie kontrolera:</span><span class="sxs-lookup"><span data-stu-id="7652f-315">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="7652f-316">Zastąp `[controller]` nazwą kontrolera, którą Konwencją jest nazwa klasy kontrolera minus sufiks "Controller".</span><span class="sxs-lookup"><span data-stu-id="7652f-316">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="7652f-317">Dla tego przykładu nazwa klasy kontrolera to **TodoItems**Controller, więc nazwa kontrolera to "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="7652f-317">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="7652f-318">W ASP.NET Core [routingu](xref:mvc/controllers/routing) jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="7652f-318">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="7652f-319">Jeśli `[HttpGet]` atrybut ma szablon trasy (na przykład `[HttpGet("products")]`), Dodaj go do ścieżki.</span><span class="sxs-lookup"><span data-stu-id="7652f-319">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="7652f-320">Ten przykład nie używa szablonu.</span><span class="sxs-lookup"><span data-stu-id="7652f-320">This sample doesn't use a template.</span></span> <span data-ttu-id="7652f-321">Aby uzyskać więcej informacji, zobacz temat [Routing atrybutów z atrybutami http [Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="7652f-321">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="7652f-322">W poniższej `GetTodoItem` metodzie `"{id}"` jest zmienną zastępczą dla unikatowego identyfikatora elementu do wykonania.</span><span class="sxs-lookup"><span data-stu-id="7652f-322">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="7652f-323">Gdy `GetTodoItem` jest wywoływana, wartość `"{id}"` w adresie URL jest podawana do metody w jej `id` parametrze.</span><span class="sxs-lookup"><span data-stu-id="7652f-323">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="7652f-324">Zwracane wartości</span><span class="sxs-lookup"><span data-stu-id="7652f-324">Return values</span></span>

<span data-ttu-id="7652f-325">Zwracany typ metod `GetTodoItems` i `GetTodoItem` to [ActionResult\<T> typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="7652f-325">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="7652f-326">ASP.NET Core automatycznie serializować obiektu do [formatu JSON](https://www.json.org/) i zapisuje kod JSON w treści komunikatu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="7652f-326">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="7652f-327">Kod odpowiedzi dla tego typu zwracanego to 200, przy założeniu, że nie istnieją Nieobsłużone wyjątki.</span><span class="sxs-lookup"><span data-stu-id="7652f-327">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="7652f-328">Nieobsłużone wyjątki są tłumaczone na błędy 5xx.</span><span class="sxs-lookup"><span data-stu-id="7652f-328">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="7652f-329">`ActionResult`typy zwracane mogą reprezentować szeroką gamę kodów stanu HTTP.</span><span class="sxs-lookup"><span data-stu-id="7652f-329">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="7652f-330">Na przykład `GetTodoItem` może zwracać dwie różne wartości stanu:</span><span class="sxs-lookup"><span data-stu-id="7652f-330">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="7652f-331">Jeśli żaden element nie jest zgodny z żądanym IDENTYFIKATORem, metoda zwraca 404 kod błędu [NOTFOUND](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) .</span><span class="sxs-lookup"><span data-stu-id="7652f-331">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="7652f-332">W przeciwnym razie metoda zwraca 200 z treścią odpowiedzi JSON.</span><span class="sxs-lookup"><span data-stu-id="7652f-332">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="7652f-333">Zwracanie `item` wyników w odpowiedzi HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="7652f-333">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="7652f-334">Metoda PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="7652f-334">The PutTodoItem method</span></span>

<span data-ttu-id="7652f-335">Badanie `PutTodoItem` metody:</span><span class="sxs-lookup"><span data-stu-id="7652f-335">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="7652f-336">`PutTodoItem`jest podobny do `PostTodoItem`, z tą różnicą, że używa protokołu HTTP Put.</span><span class="sxs-lookup"><span data-stu-id="7652f-336">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="7652f-337">Odpowiedź to [204 (brak zawartości)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="7652f-337">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="7652f-338">Zgodnie ze specyfikacją protokołu HTTP żądanie PUT wymaga, aby klient wysłał całą zaktualizowaną jednostkę, a nie tylko te zmiany.</span><span class="sxs-lookup"><span data-stu-id="7652f-338">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="7652f-339">Aby zapewnić obsługę częściowych aktualizacji, użyj [poprawki http](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="7652f-339">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="7652f-340">Jeśli wystąpi błąd podczas wywoływania `PutTodoItem`, wywołaj `GET` , aby upewnić się, że w bazie danych znajduje się element.</span><span class="sxs-lookup"><span data-stu-id="7652f-340">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="7652f-341">Testowanie metody PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="7652f-341">Test the PutTodoItem method</span></span>

<span data-ttu-id="7652f-342">Ten przykład korzysta z bazy danych w pamięci, która musi zostać zainicjowana za każdym razem, gdy aplikacja zostanie uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="7652f-342">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="7652f-343">Przed wykonaniem wywołania PUT musi istnieć element w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="7652f-343">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="7652f-344">Wywołaj polecenie GET, aby upewnić się, że w bazie danych znajduje się element, przed wykonaniem wywołania PUT.</span><span class="sxs-lookup"><span data-stu-id="7652f-344">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="7652f-345">Zaktualizuj element do wykonania o IDENTYFIKATORze 1 i ustaw jego nazwę na "Źródło danych":</span><span class="sxs-lookup"><span data-stu-id="7652f-345">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="7652f-346">Na poniższej ilustracji przedstawiono aktualizację programu Poster:</span><span class="sxs-lookup"><span data-stu-id="7652f-346">The following image shows the Postman update:</span></span>

![Konsola programu Poster pokazująca odpowiedź 204 (brak zawartości)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="7652f-348">Metoda DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="7652f-348">The DeleteTodoItem method</span></span>

<span data-ttu-id="7652f-349">Badanie `DeleteTodoItem` metody:</span><span class="sxs-lookup"><span data-stu-id="7652f-349">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="7652f-350">Testowanie metody DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="7652f-350">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="7652f-351">Użyj programu Poster, aby usunąć element do wykonania:</span><span class="sxs-lookup"><span data-stu-id="7652f-351">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="7652f-352">Ustaw metodę na `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="7652f-352">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="7652f-353">Ustaw identyfikator URI obiektu do usunięcia (na przykład `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="7652f-353">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="7652f-354">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="7652f-354">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="7652f-355">Zapobiegaj za pośrednictwem księgowania</span><span class="sxs-lookup"><span data-stu-id="7652f-355">Prevent over-posting</span></span>

<span data-ttu-id="7652f-356">Obecnie Przykładowa aplikacja uwidacznia cały `TodoItem` obiekt.</span><span class="sxs-lookup"><span data-stu-id="7652f-356">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="7652f-357">Aplikacje produkcji zwykle ograniczają dane wejściowe i zwracane przy użyciu podzestawu modelu.</span><span class="sxs-lookup"><span data-stu-id="7652f-357">Productions apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="7652f-358">Istnieje wiele powodów związanych z tym, a zabezpieczenia są głównymi.</span><span class="sxs-lookup"><span data-stu-id="7652f-358">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="7652f-359">Podzestaw modelu jest zwykle określany jako obiekt Transfer danych (DTO), model wejściowy lub model widoku.</span><span class="sxs-lookup"><span data-stu-id="7652f-359">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="7652f-360">**DTO** jest używany w tym artykule.</span><span class="sxs-lookup"><span data-stu-id="7652f-360">**DTO** is used in this article.</span></span>

<span data-ttu-id="7652f-361">DTO może służyć do:</span><span class="sxs-lookup"><span data-stu-id="7652f-361">A DTO may be used to:</span></span>

* <span data-ttu-id="7652f-362">Zablokuj nadmierne księgowanie.</span><span class="sxs-lookup"><span data-stu-id="7652f-362">Prevent over-posting.</span></span>
* <span data-ttu-id="7652f-363">Ukryj właściwości, które nie powinny być wyświetlane dla klientów.</span><span class="sxs-lookup"><span data-stu-id="7652f-363">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="7652f-364">Pomiń niektóre właściwości, aby zmniejszyć rozmiar ładunku.</span><span class="sxs-lookup"><span data-stu-id="7652f-364">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="7652f-365">Spłaszcz wykresy obiektów zawierające obiekty zagnieżdżone.</span><span class="sxs-lookup"><span data-stu-id="7652f-365">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="7652f-366">Spłaszczone wykresy obiektów mogą być wygodniejsze dla klientów.</span><span class="sxs-lookup"><span data-stu-id="7652f-366">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="7652f-367">Aby zademonstrować podejście DTO, zaktualizuj `TodoItem` klasę w celu uwzględnienia pola tajnego:</span><span class="sxs-lookup"><span data-stu-id="7652f-367">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="7652f-368">Pole tajne musi być ukryte w tej aplikacji, ale aplikacja administracyjna mogła ją uwidocznić.</span><span class="sxs-lookup"><span data-stu-id="7652f-368">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="7652f-369">Sprawdź, czy można opublikować i pobrać pole tajne.</span><span class="sxs-lookup"><span data-stu-id="7652f-369">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="7652f-370">Utwórz model DTO:</span><span class="sxs-lookup"><span data-stu-id="7652f-370">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="7652f-371">Zaktualizuj, `TodoItemsController` aby użyć `TodoItemDTO`:</span><span class="sxs-lookup"><span data-stu-id="7652f-371">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="7652f-372">Upewnij się, że nie można opublikować lub pobrać pola tajnego.</span><span class="sxs-lookup"><span data-stu-id="7652f-372">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="7652f-373">Wywoływanie interfejsu API sieci Web przy użyciu języka JavaScript</span><span class="sxs-lookup"><span data-stu-id="7652f-373">Call the web API with JavaScript</span></span>

<span data-ttu-id="7652f-374">Zobacz [Samouczek: wywoływanie interfejsu API sieci web ASP.NET Core przy użyciu języka JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="7652f-374">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7652f-375">Ten samouczek zawiera informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="7652f-375">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="7652f-376">Utwórz projekt interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="7652f-376">Create a web API project.</span></span>
> * <span data-ttu-id="7652f-377">Dodaj klasę modelu i kontekst bazy danych.</span><span class="sxs-lookup"><span data-stu-id="7652f-377">Add a model class and a database context.</span></span>
> * <span data-ttu-id="7652f-378">Dodaj kontroler.</span><span class="sxs-lookup"><span data-stu-id="7652f-378">Add a controller.</span></span>
> * <span data-ttu-id="7652f-379">Dodaj metody CRUD.</span><span class="sxs-lookup"><span data-stu-id="7652f-379">Add CRUD methods.</span></span>
> * <span data-ttu-id="7652f-380">Skonfiguruj ścieżki routingu i adresów URL.</span><span class="sxs-lookup"><span data-stu-id="7652f-380">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="7652f-381">Określ wartości zwracane.</span><span class="sxs-lookup"><span data-stu-id="7652f-381">Specify return values.</span></span>
> * <span data-ttu-id="7652f-382">Wywołaj interfejs API sieci Web za pomocą programu Poster.</span><span class="sxs-lookup"><span data-stu-id="7652f-382">Call the web API with Postman.</span></span>
> * <span data-ttu-id="7652f-383">Wywołaj interfejs API sieci Web za pomocą języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="7652f-383">Call the web API with JavaScript.</span></span>

<span data-ttu-id="7652f-384">Na końcu znajduje się internetowy interfejs API, który może zarządzać elementami do wykonania przechowywanymi w relacyjnej bazie danych.</span><span class="sxs-lookup"><span data-stu-id="7652f-384">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="7652f-385">Omówienie</span><span class="sxs-lookup"><span data-stu-id="7652f-385">Overview</span></span>

<span data-ttu-id="7652f-386">Ten samouczek tworzy następujący interfejs API:</span><span class="sxs-lookup"><span data-stu-id="7652f-386">This tutorial creates the following API:</span></span>

|<span data-ttu-id="7652f-387">Interfejs API</span><span class="sxs-lookup"><span data-stu-id="7652f-387">API</span></span> | <span data-ttu-id="7652f-388">Opis</span><span class="sxs-lookup"><span data-stu-id="7652f-388">Description</span></span> | <span data-ttu-id="7652f-389">Treść żądania</span><span class="sxs-lookup"><span data-stu-id="7652f-389">Request body</span></span> | <span data-ttu-id="7652f-390">Treść odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="7652f-390">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="7652f-391">Pobierz/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="7652f-391">GET /api/TodoItems</span></span> | <span data-ttu-id="7652f-392">Pobierz wszystkie elementy do wykonania</span><span class="sxs-lookup"><span data-stu-id="7652f-392">Get all to-do items</span></span> | <span data-ttu-id="7652f-393">Brak</span><span class="sxs-lookup"><span data-stu-id="7652f-393">None</span></span> | <span data-ttu-id="7652f-394">Tablica elementów do wykonania</span><span class="sxs-lookup"><span data-stu-id="7652f-394">Array of to-do items</span></span>|
|<span data-ttu-id="7652f-395">Pobierz/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="7652f-395">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="7652f-396">Pobieranie elementu według identyfikatora</span><span class="sxs-lookup"><span data-stu-id="7652f-396">Get an item by ID</span></span> | <span data-ttu-id="7652f-397">Brak</span><span class="sxs-lookup"><span data-stu-id="7652f-397">None</span></span> | <span data-ttu-id="7652f-398">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="7652f-398">To-do item</span></span>|
|<span data-ttu-id="7652f-399">Opublikuj/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="7652f-399">POST /api/TodoItems</span></span> | <span data-ttu-id="7652f-400">Dodaj nowy element</span><span class="sxs-lookup"><span data-stu-id="7652f-400">Add a new item</span></span> | <span data-ttu-id="7652f-401">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="7652f-401">To-do item</span></span> | <span data-ttu-id="7652f-402">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="7652f-402">To-do item</span></span> |
|<span data-ttu-id="7652f-403">Umieść/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="7652f-403">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="7652f-404">Aktualizowanie istniejącego elementu&nbsp;</span><span class="sxs-lookup"><span data-stu-id="7652f-404">Update an existing item &nbsp;</span></span> | <span data-ttu-id="7652f-405">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="7652f-405">To-do item</span></span> | <span data-ttu-id="7652f-406">Brak</span><span class="sxs-lookup"><span data-stu-id="7652f-406">None</span></span> |
|<span data-ttu-id="7652f-407">Usuń/api/TodoItems/{id} &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="7652f-407">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="7652f-408">Usuń element &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="7652f-408">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="7652f-409">Brak</span><span class="sxs-lookup"><span data-stu-id="7652f-409">None</span></span> | <span data-ttu-id="7652f-410">Brak</span><span class="sxs-lookup"><span data-stu-id="7652f-410">None</span></span>|

<span data-ttu-id="7652f-411">Na poniższym diagramie przedstawiono projekt aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7652f-411">The following diagram shows the design of the app.</span></span>

![Klient jest reprezentowany przez pole po lewej stronie.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="7652f-417">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="7652f-417">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7652f-418">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7652f-418">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="7652f-419">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7652f-419">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7652f-420">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="7652f-420">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="7652f-421">Tworzenie projektu sieci Web</span><span class="sxs-lookup"><span data-stu-id="7652f-421">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7652f-422">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7652f-422">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7652f-423">Z menu **plik** wybierz pozycję **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="7652f-423">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="7652f-424">Wybierz szablon **aplikacja sieci Web ASP.NET Core** a następnie kliknij przycisk **dalej**.</span><span class="sxs-lookup"><span data-stu-id="7652f-424">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="7652f-425">Nazwij projekt *TodoApi* i kliknij pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="7652f-425">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="7652f-426">W oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** upewnij się, że wybrano opcję **.net Core** i **ASP.NET Core 2,2** .</span><span class="sxs-lookup"><span data-stu-id="7652f-426">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="7652f-427">Wybierz szablon **interfejsu API** i kliknij przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="7652f-427">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="7652f-428">**Nie** zaznaczaj opcji **Włącz obsługę platformy Docker**.</span><span class="sxs-lookup"><span data-stu-id="7652f-428">**Don't** select **Enable Docker Support**.</span></span>

![Okno dialogowe programu VS New Project](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="7652f-430">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7652f-430">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7652f-431">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="7652f-431">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="7652f-432">Zmień katalog (`cd`) do folderu, który będzie zawierać folder projektu.</span><span class="sxs-lookup"><span data-stu-id="7652f-432">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="7652f-433">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="7652f-433">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="7652f-434">Te polecenia tworzą nowy projekt internetowego interfejsu API i otwierają nowe wystąpienie Visual Studio Code w nowym folderze projektu.</span><span class="sxs-lookup"><span data-stu-id="7652f-434">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="7652f-435">Gdy zostanie wyświetlone okno dialogowe z pytaniem, czy chcesz dodać wymagane zasoby do projektu, wybierz opcję **tak**.</span><span class="sxs-lookup"><span data-stu-id="7652f-435">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7652f-436">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="7652f-436">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="7652f-437">Wybierz pozycję **plik** > **nowe rozwiązanie**.</span><span class="sxs-lookup"><span data-stu-id="7652f-437">Select **File** > **New Solution**.</span></span>

  ![macOS nowe rozwiązanie](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="7652f-439">Wybierz pozycję **interfejs API** > **App** > > **Next**aplikacji **.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="7652f-439">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![okno dialogowe nowego projektu macOS](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="7652f-441">W oknie dialogowym **Konfigurowanie nowego interfejsu API sieci Web ASP.NET Core** zaakceptuj domyślną **platformę docelową** programu \**.NET Core 2,2*.</span><span class="sxs-lookup"><span data-stu-id="7652f-441">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="7652f-442">Wprowadź *TodoApi* jako **nazwę projektu** , a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="7652f-442">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![okno dialogowe konfiguracji](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="7652f-444">Testowanie interfejsu API</span><span class="sxs-lookup"><span data-stu-id="7652f-444">Test the API</span></span>

<span data-ttu-id="7652f-445">Szablon projektu tworzy `values` interfejs API.</span><span class="sxs-lookup"><span data-stu-id="7652f-445">The project template creates a `values` API.</span></span> <span data-ttu-id="7652f-446">Wywołaj `Get` metodę z przeglądarki, aby przetestować aplikację.</span><span class="sxs-lookup"><span data-stu-id="7652f-446">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7652f-447">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7652f-447">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7652f-448">Naciśnij klawisze CTRL + F5, aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="7652f-448">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="7652f-449">Program Visual Studio uruchamia przeglądarkę i przechodzi do `https://localhost:<port>/api/values`lokalizacji, gdzie `<port>` jest losowo wybierany numer portu.</span><span class="sxs-lookup"><span data-stu-id="7652f-449">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="7652f-450">Jeśli zostanie wyświetlone okno dialogowe z pytaniem, czy należy zaufać certyfikatowi IIS Express, wybierz pozycję **tak**.</span><span class="sxs-lookup"><span data-stu-id="7652f-450">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="7652f-451">W wyświetlonym oknie dialogowym **ostrzeżenia o zabezpieczeniach** wybierz pozycję **tak**.</span><span class="sxs-lookup"><span data-stu-id="7652f-451">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7652f-452">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7652f-452">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7652f-453">Naciśnij klawisze CTRL + F5, aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="7652f-453">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="7652f-454">W przeglądarce przejdź do następującego adresu URL: `https://localhost:5001/api/values`.</span><span class="sxs-lookup"><span data-stu-id="7652f-454">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7652f-455">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="7652f-455">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="7652f-456">Wybierz pozycję **Uruchom** > **Rozpocznij debugowanie** , aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="7652f-456">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="7652f-457">Visual Studio dla komputerów Mac uruchamia przeglądarkę i nawiguje do `https://localhost:<port>`, gdzie `<port>` jest losowo wybranym numerem portu.</span><span class="sxs-lookup"><span data-stu-id="7652f-457">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="7652f-458">Zwracany jest błąd HTTP 404 (nie znaleziono).</span><span class="sxs-lookup"><span data-stu-id="7652f-458">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="7652f-459">Dołącz `/api/values` do adresu URL (Zmień adres URL na `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="7652f-459">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="7652f-460">Zostanie zwrócony następujący kod JSON:</span><span class="sxs-lookup"><span data-stu-id="7652f-460">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="7652f-461">Dodaj klasę modelu</span><span class="sxs-lookup"><span data-stu-id="7652f-461">Add a model class</span></span>

<span data-ttu-id="7652f-462">*Model* to zestaw klas, które reprezentują dane zarządzane przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="7652f-462">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="7652f-463">Model tej aplikacji jest pojedynczą `TodoItem` klasą.</span><span class="sxs-lookup"><span data-stu-id="7652f-463">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7652f-464">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7652f-464">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7652f-465">W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt.</span><span class="sxs-lookup"><span data-stu-id="7652f-465">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="7652f-466">Wybierz pozycję **Dodaj** > **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="7652f-466">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="7652f-467">Nazwij *modele*folderów.</span><span class="sxs-lookup"><span data-stu-id="7652f-467">Name the folder *Models*.</span></span>

* <span data-ttu-id="7652f-468">Kliknij prawym przyciskiem myszy folder *modele* i wybierz polecenie **Dodaj** > **klasę**.</span><span class="sxs-lookup"><span data-stu-id="7652f-468">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="7652f-469">Nadaj klasie nazwę *TodoItem* i wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="7652f-469">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="7652f-470">Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="7652f-470">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7652f-471">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7652f-471">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7652f-472">Dodaj folder o nazwie *models*.</span><span class="sxs-lookup"><span data-stu-id="7652f-472">Add a folder named *Models*.</span></span>

* <span data-ttu-id="7652f-473">Dodaj `TodoItem` klasę do folderu *models* o następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="7652f-473">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7652f-474">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="7652f-474">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="7652f-475">Kliknij prawym przyciskiem myszy projekt.</span><span class="sxs-lookup"><span data-stu-id="7652f-475">Right-click the project.</span></span> <span data-ttu-id="7652f-476">Wybierz pozycję **Dodaj** > **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="7652f-476">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="7652f-477">Nazwij *modele*folderów.</span><span class="sxs-lookup"><span data-stu-id="7652f-477">Name the folder *Models*.</span></span>

  ![Nowy folder](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="7652f-479">Kliknij prawym przyciskiem myszy folder *modele* , a następnie wybierz pozycję **Dodaj** > **nowy plik** > **ogólna** > **pusta Klasa**.</span><span class="sxs-lookup"><span data-stu-id="7652f-479">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="7652f-480">Nazwij klasę *TodoItem*, a następnie kliknij pozycję **New (nowy**).</span><span class="sxs-lookup"><span data-stu-id="7652f-480">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="7652f-481">Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="7652f-481">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="7652f-482">`Id` Właściwość działa jako unikatowy klucz w relacyjnej bazie danych.</span><span class="sxs-lookup"><span data-stu-id="7652f-482">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="7652f-483">Klasy modelu mogą przejść do dowolnego miejsca w projekcie, ale folder *modele* jest używany przez Konwencję.</span><span class="sxs-lookup"><span data-stu-id="7652f-483">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="7652f-484">Dodawanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="7652f-484">Add a database context</span></span>

<span data-ttu-id="7652f-485">*Kontekst bazy danych* jest główną klasą, która koordynuje Entity Framework funkcji dla modelu danych.</span><span class="sxs-lookup"><span data-stu-id="7652f-485">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="7652f-486">Ta klasa jest tworzona przez wyprowadzanie z `Microsoft.EntityFrameworkCore.DbContext` klasy.</span><span class="sxs-lookup"><span data-stu-id="7652f-486">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7652f-487">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7652f-487">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7652f-488">Kliknij prawym przyciskiem myszy folder *modele* i wybierz polecenie **Dodaj** > **klasę**.</span><span class="sxs-lookup"><span data-stu-id="7652f-488">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="7652f-489">Nadaj klasie nazwę *TodoContext* i kliknij przycisk **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="7652f-489">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7652f-490">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="7652f-490">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="7652f-491">Dodaj `TodoContext` klasę do folderu *models* .</span><span class="sxs-lookup"><span data-stu-id="7652f-491">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="7652f-492">Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="7652f-492">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="7652f-493">Rejestrowanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="7652f-493">Register the database context</span></span>

<span data-ttu-id="7652f-494">W ASP.NET Core usługi, takie jak kontekst bazy danych, muszą być zarejestrowane z kontenerem [iniekcji zależności (di)](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="7652f-494">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="7652f-495">Kontener udostępnia usługę kontrolerom.</span><span class="sxs-lookup"><span data-stu-id="7652f-495">The container provides the service to controllers.</span></span>

<span data-ttu-id="7652f-496">Zaktualizuj *Startup.cs* o następujący wyróżniony kod:</span><span class="sxs-lookup"><span data-stu-id="7652f-496">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="7652f-497">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="7652f-497">The preceding code:</span></span>

* <span data-ttu-id="7652f-498">Usuwa nieużywane `using` deklaracje.</span><span class="sxs-lookup"><span data-stu-id="7652f-498">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="7652f-499">Dodaje kontekst bazy danych do kontenera DI.</span><span class="sxs-lookup"><span data-stu-id="7652f-499">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="7652f-500">Określa, że kontekst bazy danych będzie używać bazy danych w pamięci.</span><span class="sxs-lookup"><span data-stu-id="7652f-500">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="7652f-501">Dodawanie kontrolera</span><span class="sxs-lookup"><span data-stu-id="7652f-501">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7652f-502">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7652f-502">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7652f-503">Kliknij prawym przyciskiem myszy folder *controllers* .</span><span class="sxs-lookup"><span data-stu-id="7652f-503">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="7652f-504">Wybierz pozycję **Dodaj** > **nowy element**.</span><span class="sxs-lookup"><span data-stu-id="7652f-504">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="7652f-505">W oknie dialogowym **Dodaj nowy element** wybierz szablon **Klasa kontrolera interfejsu API** .</span><span class="sxs-lookup"><span data-stu-id="7652f-505">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="7652f-506">Nadaj klasie nazwę *TodoController*i wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="7652f-506">Name the class *TodoController*, and select **Add**.</span></span>

  ![Okno dialogowe Dodawanie nowego elementu z kontrolerem w polu wyszukiwania i wybranym kontrolerem interfejsu API sieci Web](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7652f-508">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="7652f-508">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="7652f-509">W folderze *controllers* Utwórz klasę o nazwie `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="7652f-509">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="7652f-510">Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="7652f-510">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="7652f-511">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="7652f-511">The preceding code:</span></span>

* <span data-ttu-id="7652f-512">Definiuje klasę kontrolera interfejsu API bez metod.</span><span class="sxs-lookup"><span data-stu-id="7652f-512">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="7652f-513">Oznacza klasę [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) atrybutem.</span><span class="sxs-lookup"><span data-stu-id="7652f-513">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="7652f-514">Ten atrybut wskazuje, że kontroler odpowiada na żądania interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="7652f-514">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="7652f-515">Aby uzyskać informacje o określonych zachowaniach, które włącza atrybut <xref:web-api/index>, zobacz.</span><span class="sxs-lookup"><span data-stu-id="7652f-515">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="7652f-516">Używa funkcji DI do iniekcji kontekstu bazy`TodoContext`danych () do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="7652f-516">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="7652f-517">Kontekst bazy danych jest używany w każdej z metod [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) w kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="7652f-517">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="7652f-518">Dodaje element o nazwie `Item1` do bazy danych, jeśli baza danych jest pusta.</span><span class="sxs-lookup"><span data-stu-id="7652f-518">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="7652f-519">Ten kod znajduje się w konstruktorze, więc jest uruchamiany za każdym razem, gdy istnieje nowe żądanie HTTP.</span><span class="sxs-lookup"><span data-stu-id="7652f-519">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="7652f-520">Jeśli usuniesz wszystkie elementy, Konstruktor zostanie ponownie utworzony `Item1` przy następnym wywołaniu metody interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="7652f-520">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="7652f-521">Dlatego może wyglądać podobnie jak usunięcie nie zadziałało, gdy rzeczywiście zadziałało.</span><span class="sxs-lookup"><span data-stu-id="7652f-521">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="7652f-522">Dodawanie metod get</span><span class="sxs-lookup"><span data-stu-id="7652f-522">Add Get methods</span></span>

<span data-ttu-id="7652f-523">Aby udostępnić interfejs API, który pobiera elementy do wykonania, Dodaj następujące metody do `TodoController` klasy:</span><span class="sxs-lookup"><span data-stu-id="7652f-523">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="7652f-524">Te metody implementują dwa punkty końcowe GET:</span><span class="sxs-lookup"><span data-stu-id="7652f-524">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="7652f-525">Zatrzymaj aplikację, jeśli jest nadal uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="7652f-525">Stop the app if it's still running.</span></span> <span data-ttu-id="7652f-526">Następnie uruchom ją ponownie, aby uwzględnić najnowsze zmiany.</span><span class="sxs-lookup"><span data-stu-id="7652f-526">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="7652f-527">Przetestuj aplikację, wywołując dwa punkty końcowe z przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="7652f-527">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="7652f-528">Przykład:</span><span class="sxs-lookup"><span data-stu-id="7652f-528">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="7652f-529">Następująca odpowiedź HTTP jest generowana przez wywołanie `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="7652f-529">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="7652f-530">Ścieżki routingu i adresów URL</span><span class="sxs-lookup"><span data-stu-id="7652f-530">Routing and URL paths</span></span>

<span data-ttu-id="7652f-531">Ten [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) atrybut oznacza metodę, która reaguje na żądanie HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="7652f-531">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="7652f-532">Ścieżka adresu URL dla każdej metody jest zbudowana w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="7652f-532">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="7652f-533">Zacznij od ciągu szablonu w `Route` atrybucie kontrolera:</span><span class="sxs-lookup"><span data-stu-id="7652f-533">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="7652f-534">Zastąp `[controller]` nazwą kontrolera, którą Konwencją jest nazwa klasy kontrolera minus sufiks "Controller".</span><span class="sxs-lookup"><span data-stu-id="7652f-534">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="7652f-535">W przypadku tego przykładu nazwa klasy kontrolera to kontroler do **zrobienia**, więc nazwa kontrolera to "do zrobienia".</span><span class="sxs-lookup"><span data-stu-id="7652f-535">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="7652f-536">W ASP.NET Core [routingu](xref:mvc/controllers/routing) jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="7652f-536">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="7652f-537">Jeśli `[HttpGet]` atrybut ma szablon trasy (na przykład `[HttpGet("products")]`), Dodaj go do ścieżki.</span><span class="sxs-lookup"><span data-stu-id="7652f-537">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="7652f-538">Ten przykład nie używa szablonu.</span><span class="sxs-lookup"><span data-stu-id="7652f-538">This sample doesn't use a template.</span></span> <span data-ttu-id="7652f-539">Aby uzyskać więcej informacji, zobacz temat [Routing atrybutów z atrybutami http [Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="7652f-539">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="7652f-540">W poniższej `GetTodoItem` metodzie `"{id}"` jest zmienną zastępczą dla unikatowego identyfikatora elementu do wykonania.</span><span class="sxs-lookup"><span data-stu-id="7652f-540">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="7652f-541">Gdy `GetTodoItem` jest wywoływana, wartość `"{id}"` w adresie URL jest podawana do metody w jej`id` parametrze.</span><span class="sxs-lookup"><span data-stu-id="7652f-541">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="7652f-542">Zwracane wartości</span><span class="sxs-lookup"><span data-stu-id="7652f-542">Return values</span></span>

<span data-ttu-id="7652f-543">Zwracany typ metod `GetTodoItems` i `GetTodoItem` to [ActionResult\<T> typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="7652f-543">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="7652f-544">ASP.NET Core automatycznie serializować obiektu do [formatu JSON](https://www.json.org/) i zapisuje kod JSON w treści komunikatu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="7652f-544">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="7652f-545">Kod odpowiedzi dla tego typu zwracanego to 200, przy założeniu, że nie istnieją Nieobsłużone wyjątki.</span><span class="sxs-lookup"><span data-stu-id="7652f-545">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="7652f-546">Nieobsłużone wyjątki są tłumaczone na błędy 5xx.</span><span class="sxs-lookup"><span data-stu-id="7652f-546">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="7652f-547">`ActionResult`typy zwracane mogą reprezentować szeroką gamę kodów stanu HTTP.</span><span class="sxs-lookup"><span data-stu-id="7652f-547">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="7652f-548">Na przykład `GetTodoItem` może zwracać dwie różne wartości stanu:</span><span class="sxs-lookup"><span data-stu-id="7652f-548">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="7652f-549">Jeśli żaden element nie jest zgodny z żądanym IDENTYFIKATORem, metoda zwraca 404 kod błędu [NOTFOUND](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) .</span><span class="sxs-lookup"><span data-stu-id="7652f-549">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="7652f-550">W przeciwnym razie metoda zwraca 200 z treścią odpowiedzi JSON.</span><span class="sxs-lookup"><span data-stu-id="7652f-550">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="7652f-551">Zwracanie `item` wyników w odpowiedzi HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="7652f-551">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="7652f-552">Testowanie metody GetTodoItems</span><span class="sxs-lookup"><span data-stu-id="7652f-552">Test the GetTodoItems method</span></span>

<span data-ttu-id="7652f-553">Ten samouczek używa programu do testowania interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="7652f-553">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="7652f-554">Zainstaluj program [Poster](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="7652f-554">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="7652f-555">Uruchom aplikację internetową.</span><span class="sxs-lookup"><span data-stu-id="7652f-555">Start the web app.</span></span>
* <span data-ttu-id="7652f-556">Uruchom wpis.</span><span class="sxs-lookup"><span data-stu-id="7652f-556">Start Postman.</span></span>
* <span data-ttu-id="7652f-557">Wyłącz **weryfikację certyfikatu SSL**.</span><span class="sxs-lookup"><span data-stu-id="7652f-557">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7652f-558">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7652f-558">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7652f-559">W obszarze **Ustawienia** **pliku** > (karta**Ogólne** ) Wyłącz **weryfikację certyfikatu SSL**.</span><span class="sxs-lookup"><span data-stu-id="7652f-559">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7652f-560">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="7652f-560">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="7652f-561">Z poziomu**preferencji** programu **Poster** > (karta**Ogólne** ) Wyłącz **weryfikację certyfikatu SSL**.</span><span class="sxs-lookup"><span data-stu-id="7652f-561">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="7652f-562">Alternatywnie wybierz klucz i wybierz pozycję **Ustawienia**, a następnie wyłącz weryfikację certyfikatu SSL.</span><span class="sxs-lookup"><span data-stu-id="7652f-562">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="7652f-563">Po przetestowaniu kontrolera ponownie Włącz weryfikację certyfikatu SSL.</span><span class="sxs-lookup"><span data-stu-id="7652f-563">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="7652f-564">Utwórz nowe żądanie.</span><span class="sxs-lookup"><span data-stu-id="7652f-564">Create a new request.</span></span>
  * <span data-ttu-id="7652f-565">Ustaw metodę HTTP, aby **uzyskać**.</span><span class="sxs-lookup"><span data-stu-id="7652f-565">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="7652f-566">Ustaw adres URL żądania `https://localhost:<port>/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="7652f-566">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="7652f-567">Na przykład `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="7652f-567">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="7652f-568">Ustaw **dwa widoki okienka** w programie Poster.</span><span class="sxs-lookup"><span data-stu-id="7652f-568">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="7652f-569">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="7652f-569">Select **Send**.</span></span>

![Ogłoś przy użyciu żądania GET](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="7652f-571">Dodawanie metody Create</span><span class="sxs-lookup"><span data-stu-id="7652f-571">Add a Create method</span></span>

<span data-ttu-id="7652f-572">Dodaj następującą `PostTodoItem` metodę w obszarze *controllers/TodoController. cs*:</span><span class="sxs-lookup"><span data-stu-id="7652f-572">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="7652f-573">Poprzedni kod jest metodą POST protokołu HTTP, jak wskazano w [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) atrybucie.</span><span class="sxs-lookup"><span data-stu-id="7652f-573">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="7652f-574">Metoda pobiera wartość elementu do wykonania z treści żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="7652f-574">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="7652f-575">`CreatedAtAction` Metoda:</span><span class="sxs-lookup"><span data-stu-id="7652f-575">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="7652f-576">Zwraca kod stanu HTTP 201, jeśli powodzenie.</span><span class="sxs-lookup"><span data-stu-id="7652f-576">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="7652f-577">HTTP 201 to standardowa odpowiedź dla metody POST protokołu HTTP, która tworzy nowy zasób na serwerze.</span><span class="sxs-lookup"><span data-stu-id="7652f-577">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="7652f-578">Dodaje `Location` nagłówek do odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="7652f-578">Adds a `Location` header to the response.</span></span> <span data-ttu-id="7652f-579">`Location` Nagłówek określa identyfikator URI nowo utworzonego elementu do wykonania.</span><span class="sxs-lookup"><span data-stu-id="7652f-579">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="7652f-580">Aby uzyskać więcej informacji, zobacz [10.2.2 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="7652f-580">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="7652f-581">Odwołuje `GetTodoItem` się do akcji tworzenia `Location` identyfikatora URI nagłówka.</span><span class="sxs-lookup"><span data-stu-id="7652f-581">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="7652f-582">Słowo kluczowe `nameof` języka C# służy do zapobiegania twardemu kodowaniu nazwy akcji w `CreatedAtAction` wywołaniu.</span><span class="sxs-lookup"><span data-stu-id="7652f-582">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="7652f-583">Testowanie metody PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="7652f-583">Test the PostTodoItem method</span></span>

* <span data-ttu-id="7652f-584">Skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="7652f-584">Build the project.</span></span>
* <span data-ttu-id="7652f-585">W programie Poster ustaw metodę HTTP na `POST`.</span><span class="sxs-lookup"><span data-stu-id="7652f-585">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="7652f-586">Wybierz kartę **Treść**.</span><span class="sxs-lookup"><span data-stu-id="7652f-586">Select the **Body** tab.</span></span>
* <span data-ttu-id="7652f-587">Wybierz przycisk radiowy **RAW** .</span><span class="sxs-lookup"><span data-stu-id="7652f-587">Select the **raw** radio button.</span></span>
* <span data-ttu-id="7652f-588">Ustaw typ na **JSON (Application/JSON)**.</span><span class="sxs-lookup"><span data-stu-id="7652f-588">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="7652f-589">W treści żądania wprowadź kod JSON dla elementu do wykonania:</span><span class="sxs-lookup"><span data-stu-id="7652f-589">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="7652f-590">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="7652f-590">Select **Send**.</span></span>

  ![Ogłoś przy użyciu żądania Create](first-web-api/_static/create.png)

  <span data-ttu-id="7652f-592">Jeśli wystąpi błąd 405 metody niedozwolonej, jest to prawdopodobnie wynik niekompilowania projektu po dodaniu `PostTodoItem` metody.</span><span class="sxs-lookup"><span data-stu-id="7652f-592">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="7652f-593">Testowanie identyfikatora URI nagłówka lokalizacji</span><span class="sxs-lookup"><span data-stu-id="7652f-593">Test the location header URI</span></span>

* <span data-ttu-id="7652f-594">Wybierz kartę **nagłówki** w okienku **odpowiedź** .</span><span class="sxs-lookup"><span data-stu-id="7652f-594">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="7652f-595">Skopiuj wartość nagłówka **lokalizacji** :</span><span class="sxs-lookup"><span data-stu-id="7652f-595">Copy the **Location** header value:</span></span>

  ![Karta nagłówki w konsoli programu Poster](first-web-api/_static/pmc2.png)

* <span data-ttu-id="7652f-597">Ustaw metodę, aby uzyskać.</span><span class="sxs-lookup"><span data-stu-id="7652f-597">Set the method to GET.</span></span>
* <span data-ttu-id="7652f-598">Wklej URI (na przykład `https://localhost:5001/api/Todo/2`).</span><span class="sxs-lookup"><span data-stu-id="7652f-598">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="7652f-599">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="7652f-599">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="7652f-600">Dodawanie metody PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="7652f-600">Add a PutTodoItem method</span></span>

<span data-ttu-id="7652f-601">Dodaj następującą `PutTodoItem` metodę:</span><span class="sxs-lookup"><span data-stu-id="7652f-601">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="7652f-602">`PutTodoItem`jest podobny do `PostTodoItem`, z tą różnicą, że używa protokołu HTTP Put.</span><span class="sxs-lookup"><span data-stu-id="7652f-602">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="7652f-603">Odpowiedź to [204 (brak zawartości)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="7652f-603">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="7652f-604">Zgodnie ze specyfikacją protokołu HTTP żądanie PUT wymaga, aby klient wysłał całą zaktualizowaną jednostkę, a nie tylko te zmiany.</span><span class="sxs-lookup"><span data-stu-id="7652f-604">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="7652f-605">Aby zapewnić obsługę częściowych aktualizacji, użyj [poprawki http](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="7652f-605">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="7652f-606">Jeśli wystąpi błąd podczas wywoływania `PutTodoItem`, wywołaj `GET` , aby upewnić się, że w bazie danych znajduje się element.</span><span class="sxs-lookup"><span data-stu-id="7652f-606">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="7652f-607">Testowanie metody PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="7652f-607">Test the PutTodoItem method</span></span>

<span data-ttu-id="7652f-608">Ten przykład korzysta z bazy danych w pamięci, która musi zostać zainicjowana za każdym razem, gdy aplikacja zostanie uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="7652f-608">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="7652f-609">Przed wykonaniem wywołania PUT musi istnieć element w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="7652f-609">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="7652f-610">Wywołaj polecenie GET, aby upewnić się, że w bazie danych znajduje się element, przed wykonaniem wywołania PUT.</span><span class="sxs-lookup"><span data-stu-id="7652f-610">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="7652f-611">Zaktualizuj element do wykonania o identyfikatorze 1 i ustaw jego nazwę na "Źródło danych":</span><span class="sxs-lookup"><span data-stu-id="7652f-611">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="7652f-612">Na poniższej ilustracji przedstawiono aktualizację programu Poster:</span><span class="sxs-lookup"><span data-stu-id="7652f-612">The following image shows the Postman update:</span></span>

![Konsola programu Poster pokazująca odpowiedź 204 (brak zawartości)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="7652f-614">Dodawanie metody DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="7652f-614">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="7652f-615">Dodaj następującą `DeleteTodoItem` metodę:</span><span class="sxs-lookup"><span data-stu-id="7652f-615">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="7652f-616">`DeleteTodoItem` Odpowiedź to [204 (brak zawartości)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="7652f-616">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="7652f-617">Testowanie metody DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="7652f-617">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="7652f-618">Użyj programu Poster, aby usunąć element do wykonania:</span><span class="sxs-lookup"><span data-stu-id="7652f-618">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="7652f-619">Ustaw metodę na `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="7652f-619">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="7652f-620">Ustaw identyfikator URI obiektu do usunięcia (na przykład `https://localhost:5001/api/todo/1`).</span><span class="sxs-lookup"><span data-stu-id="7652f-620">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="7652f-621">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="7652f-621">Select **Send**.</span></span>

<span data-ttu-id="7652f-622">Przykładowa aplikacja umożliwia usunięcie wszystkich elementów.</span><span class="sxs-lookup"><span data-stu-id="7652f-622">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="7652f-623">Jednak po usunięciu ostatniego elementu jest on tworzony przez konstruktora klasy modelu przy następnym wywołaniu interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="7652f-623">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="7652f-624">Wywoływanie interfejsu API sieci Web przy użyciu języka JavaScript</span><span class="sxs-lookup"><span data-stu-id="7652f-624">Call the web API with JavaScript</span></span>

<span data-ttu-id="7652f-625">W tej sekcji zostanie dodana strona HTML, która używa języka JavaScript do wywoływania internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="7652f-625">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="7652f-626">jQuery inicjuje żądanie.</span><span class="sxs-lookup"><span data-stu-id="7652f-626">jQuery initiates the request.</span></span> <span data-ttu-id="7652f-627">Język JavaScript aktualizuje stronę ze szczegółowymi informacjami z odpowiedzi internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="7652f-627">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="7652f-628">Skonfiguruj aplikację do [obsługi plików statycznych](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) i [Włącz domyślne mapowanie plików](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) , aktualizując *Startup.cs* z następującym wyróżnionym kodem:</span><span class="sxs-lookup"><span data-stu-id="7652f-628">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="7652f-629">Utwórz folder *wwwroot* w katalogu projektu.</span><span class="sxs-lookup"><span data-stu-id="7652f-629">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="7652f-630">Dodaj plik HTML o nazwie *index. html* do katalogu *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="7652f-630">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="7652f-631">Zastąp jego zawartość następującym znacznikiem:</span><span class="sxs-lookup"><span data-stu-id="7652f-631">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="7652f-632">Dodaj plik języka JavaScript o nazwie *site. js* do katalogu *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="7652f-632">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="7652f-633">Zastąp jego zawartość następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="7652f-633">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="7652f-634">Zmiana ustawień uruchamiania projektu ASP.NET Core może być wymagana do lokalnego przetestowania strony HTML:</span><span class="sxs-lookup"><span data-stu-id="7652f-634">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="7652f-635">Otwórz *Properties\launchSettings.JSON*.</span><span class="sxs-lookup"><span data-stu-id="7652f-635">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="7652f-636">Usuń `launchUrl` właściwość, aby wymusić Otwieranie aplikacji w *indeksie. html*&mdash;plik domyślny projektu.</span><span class="sxs-lookup"><span data-stu-id="7652f-636">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="7652f-637">Ten przykład wywołuje wszystkie metody CRUD internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="7652f-637">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="7652f-638">Poniżej znajdują się wyjaśnienia wywołań interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="7652f-638">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="7652f-639">Pobierz listę elementów do wykonania</span><span class="sxs-lookup"><span data-stu-id="7652f-639">Get a list of to-do items</span></span>

<span data-ttu-id="7652f-640">jQuery wysyła żądanie HTTP GET do internetowego interfejsu API, który zwraca kod JSON reprezentujący tablicę elementów do wykonania.</span><span class="sxs-lookup"><span data-stu-id="7652f-640">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="7652f-641">Funkcja `success` wywołania zwrotnego jest wywoływana, jeśli żądanie zakończy się pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="7652f-641">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="7652f-642">W wywołaniu zwrotnym model DOM jest aktualizowany przy użyciu informacji o tym do wykonania.</span><span class="sxs-lookup"><span data-stu-id="7652f-642">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="7652f-643">Dodaj element do wykonania</span><span class="sxs-lookup"><span data-stu-id="7652f-643">Add a to-do item</span></span>

<span data-ttu-id="7652f-644">jQuery wysyła żądanie HTTP POST z elementem do wykonania w treści żądania.</span><span class="sxs-lookup"><span data-stu-id="7652f-644">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="7652f-645">Opcje `accepts` i `contentType` są ustawione na `application/json` , aby określić typ nośnika odbierany i wysyłany.</span><span class="sxs-lookup"><span data-stu-id="7652f-645">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="7652f-646">Element do wykonania jest konwertowany na format JSON przy użyciu [formatu JSON. stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="7652f-646">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="7652f-647">Gdy interfejs API zwraca kod stanu pomyślnego, `getData` funkcja jest wywoływana w celu zaktualizowania tabeli HTML.</span><span class="sxs-lookup"><span data-stu-id="7652f-647">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="7652f-648">Aktualizowanie elementu do wykonania</span><span class="sxs-lookup"><span data-stu-id="7652f-648">Update a to-do item</span></span>

<span data-ttu-id="7652f-649">Aktualizowanie elementu do wykonania jest podobne do dodawania jednego.</span><span class="sxs-lookup"><span data-stu-id="7652f-649">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="7652f-650">`url` Zmiany mające na celu dodanie unikatowego identyfikatora elementu i `type` is `PUT`.</span><span class="sxs-lookup"><span data-stu-id="7652f-650">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="7652f-651">Usuń element do wykonania</span><span class="sxs-lookup"><span data-stu-id="7652f-651">Delete a to-do item</span></span>

<span data-ttu-id="7652f-652">Usuwanie elementu do wykonania jest realizowane przez ustawienie `type` w wywołaniu AJAX `DELETE` i określenie unikatowego identyfikatora elementu w adresie URL.</span><span class="sxs-lookup"><span data-stu-id="7652f-652">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="7652f-653">Dodawanie obsługi uwierzytelniania do internetowego interfejsu API</span><span class="sxs-lookup"><span data-stu-id="7652f-653">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a><span data-ttu-id="7652f-654">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="7652f-654">Additional resources</span></span>

<span data-ttu-id="7652f-655">[Wyświetl lub Pobierz przykładowy kod dla tego samouczka](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="7652f-655">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="7652f-656">Zobacz artykuł [jak pobrać](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="7652f-656">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="7652f-657">Więcej informacji zawierają następujące zasoby:</span><span class="sxs-lookup"><span data-stu-id="7652f-657">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="7652f-658">Wersja tego samouczka usługi YouTube</span><span class="sxs-lookup"><span data-stu-id="7652f-658">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
