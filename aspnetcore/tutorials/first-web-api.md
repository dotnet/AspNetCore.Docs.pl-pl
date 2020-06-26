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
ms.openlocfilehash: 63f91086a7e9d71add7f7a5d58d96f46fa76353c
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407788"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="5b19c-103">Samouczek: Tworzenie internetowego interfejsu API za pomocą ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5b19c-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="5b19c-104">Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirka Larkin](https://twitter.com/serpent5)i [Jan Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="5b19c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="5b19c-105">Ten samouczek uczy się podstaw tworzenia interfejsu API sieci Web za pomocą ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5b19c-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5b19c-106">Ten samouczek zawiera informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="5b19c-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5b19c-107">Utwórz projekt interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="5b19c-107">Create a web API project.</span></span>
> * <span data-ttu-id="5b19c-108">Dodaj klasę modelu i kontekst bazy danych.</span><span class="sxs-lookup"><span data-stu-id="5b19c-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="5b19c-109">Tworzy szkielet kontrolera z metodami CRUD.</span><span class="sxs-lookup"><span data-stu-id="5b19c-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="5b19c-110">Skonfiguruj Routing, ścieżki URL i wartości zwracane.</span><span class="sxs-lookup"><span data-stu-id="5b19c-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="5b19c-111">Wywołaj interfejs API sieci Web za pomocą programu Poster.</span><span class="sxs-lookup"><span data-stu-id="5b19c-111">Call the web API with Postman.</span></span>

<span data-ttu-id="5b19c-112">Na końcu znajduje się internetowy interfejs API, który może zarządzać elementami do wykonania przechowywanymi w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="5b19c-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="5b19c-113">Omówienie</span><span class="sxs-lookup"><span data-stu-id="5b19c-113">Overview</span></span>

<span data-ttu-id="5b19c-114">Ten samouczek tworzy następujący interfejs API:</span><span class="sxs-lookup"><span data-stu-id="5b19c-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="5b19c-115">Interfejs API</span><span class="sxs-lookup"><span data-stu-id="5b19c-115">API</span></span> | <span data-ttu-id="5b19c-116">Opis</span><span class="sxs-lookup"><span data-stu-id="5b19c-116">Description</span></span> | <span data-ttu-id="5b19c-117">Treść żądania</span><span class="sxs-lookup"><span data-stu-id="5b19c-117">Request body</span></span> | <span data-ttu-id="5b19c-118">Treść odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="5b19c-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="5b19c-119">Pobierz wszystkie elementy do wykonania</span><span class="sxs-lookup"><span data-stu-id="5b19c-119">Get all to-do items</span></span> | <span data-ttu-id="5b19c-120">Brak</span><span class="sxs-lookup"><span data-stu-id="5b19c-120">None</span></span> | <span data-ttu-id="5b19c-121">Tablica elementów do wykonania</span><span class="sxs-lookup"><span data-stu-id="5b19c-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="5b19c-122">Pobieranie elementu według identyfikatora</span><span class="sxs-lookup"><span data-stu-id="5b19c-122">Get an item by ID</span></span> | <span data-ttu-id="5b19c-123">Brak</span><span class="sxs-lookup"><span data-stu-id="5b19c-123">None</span></span> | <span data-ttu-id="5b19c-124">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="5b19c-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="5b19c-125">Dodaj nowy element</span><span class="sxs-lookup"><span data-stu-id="5b19c-125">Add a new item</span></span> | <span data-ttu-id="5b19c-126">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="5b19c-126">To-do item</span></span> | <span data-ttu-id="5b19c-127">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="5b19c-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="5b19c-128">Aktualizowanie istniejącego elementu&nbsp;</span><span class="sxs-lookup"><span data-stu-id="5b19c-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="5b19c-129">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="5b19c-129">To-do item</span></span> | <span data-ttu-id="5b19c-130">Brak</span><span class="sxs-lookup"><span data-stu-id="5b19c-130">None</span></span> |
|<span data-ttu-id="5b19c-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="5b19c-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="5b19c-132">Usuń element &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="5b19c-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="5b19c-133">Brak</span><span class="sxs-lookup"><span data-stu-id="5b19c-133">None</span></span> | <span data-ttu-id="5b19c-134">Brak</span><span class="sxs-lookup"><span data-stu-id="5b19c-134">None</span></span>|

<span data-ttu-id="5b19c-135">Na poniższym diagramie przedstawiono projekt aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5b19c-135">The following diagram shows the design of the app.</span></span>

![Klient jest reprezentowany przez pole po lewej stronie.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="5b19c-141">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="5b19c-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5b19c-142">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5b19c-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="5b19c-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5b19c-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5b19c-144">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="5b19c-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="5b19c-145">Tworzenie projektu sieci Web</span><span class="sxs-lookup"><span data-stu-id="5b19c-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5b19c-146">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5b19c-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5b19c-147">Z menu **plik** wybierz pozycję **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="5b19c-148">Wybierz szablon **aplikacja sieci Web ASP.NET Core** a następnie kliknij przycisk **dalej**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="5b19c-149">Nazwij projekt *TodoApi* i kliknij pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="5b19c-150">W oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** upewnij się, że wybrano opcję **.net Core** i **ASP.NET Core 3,1** .</span><span class="sxs-lookup"><span data-stu-id="5b19c-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="5b19c-151">Wybierz szablon **interfejsu API** i kliknij przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-151">Select the **API** template and click **Create**.</span></span>

![Okno dialogowe programu VS New Project](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="5b19c-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5b19c-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5b19c-154">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="5b19c-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="5b19c-155">Zmień katalog ( `cd` ) do folderu, który będzie zawierać folder projektu.</span><span class="sxs-lookup"><span data-stu-id="5b19c-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="5b19c-156">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="5b19c-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="5b19c-157">Gdy zostanie wyświetlone okno dialogowe z pytaniem, czy chcesz dodać wymagane zasoby do projektu, wybierz opcję **tak**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="5b19c-158">Poprzednie polecenia:</span><span class="sxs-lookup"><span data-stu-id="5b19c-158">The preceding commands:</span></span>

  * <span data-ttu-id="5b19c-159">Tworzy nowy projekt internetowego interfejsu API i otwiera go w Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="5b19c-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="5b19c-160">Dodaje pakiety NuGet, które są wymagane w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="5b19c-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5b19c-161">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="5b19c-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="5b19c-162">Wybierz pozycję **plik** > **nowe rozwiązanie**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-162">Select **File** > **New Solution**.</span></span>

  ![macOS nowe rozwiązanie](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="5b19c-164">W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz **.NET Core**pozycję  >  **App**  >  **interfejs API**aplikacji .NET Core  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="5b19c-165">W wersji 8,6 lub nowszej wybierz pozycję **Web and Console**  >  **App**  >  **interfejs API**  >  **Next** aplikacji sieci Web i konsoli.</span><span class="sxs-lookup"><span data-stu-id="5b19c-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next** .</span></span>

  ![Wybór szablonu interfejsu API macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="5b19c-167">Upewnij się, że **platforma docelowa** jest ustawiona na **platformę .NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-167">Confirm the **Target Framework** is set to **.NET Core 3.1**.</span></span> <span data-ttu-id="5b19c-168">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-168">Select **Next**.</span></span>

  ![wybór macOS .NET Core 3,1](first-web-api-mac/_static/api_31_config.png)

* <span data-ttu-id="5b19c-170">Wprowadź *TodoApi* jako **nazwę projektu** , a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-170">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![okno dialogowe konfiguracji](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="5b19c-172">Otwórz Terminal poleceń w folderze projektu i uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="5b19c-172">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="5b19c-173">Testowanie interfejsu API</span><span class="sxs-lookup"><span data-stu-id="5b19c-173">Test the API</span></span>

<span data-ttu-id="5b19c-174">Szablon projektu tworzy `WeatherForecast` interfejs API.</span><span class="sxs-lookup"><span data-stu-id="5b19c-174">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="5b19c-175">Wywołaj `Get` metodę z przeglądarki, aby przetestować aplikację.</span><span class="sxs-lookup"><span data-stu-id="5b19c-175">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5b19c-176">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5b19c-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5b19c-177">Naciśnij klawisze CTRL + F5, aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="5b19c-177">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="5b19c-178">Program Visual Studio uruchamia przeglądarkę i przechodzi do `https://localhost:<port>/WeatherForecast` lokalizacji, gdzie `<port>` jest losowo wybierany numer portu.</span><span class="sxs-lookup"><span data-stu-id="5b19c-178">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="5b19c-179">Jeśli zostanie wyświetlone okno dialogowe z pytaniem, czy należy zaufać certyfikatowi IIS Express, wybierz pozycję **tak**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-179">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="5b19c-180">W wyświetlonym oknie dialogowym **ostrzeżenia o zabezpieczeniach** wybierz pozycję **tak**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-180">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5b19c-181">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5b19c-181">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5b19c-182">Naciśnij klawisze CTRL + F5, aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="5b19c-182">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="5b19c-183">W przeglądarce przejdź do następującego adresu URL: `https://localhost:5001/WeatherForecast` .</span><span class="sxs-lookup"><span data-stu-id="5b19c-183">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5b19c-184">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="5b19c-184">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5b19c-185">Wybierz pozycję **Uruchom**  >  **Rozpocznij debugowanie** , aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="5b19c-185">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="5b19c-186">Visual Studio dla komputerów Mac uruchamia przeglądarkę i nawiguje do `https://localhost:<port>` , gdzie `<port>` jest losowo wybranym numerem portu.</span><span class="sxs-lookup"><span data-stu-id="5b19c-186">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="5b19c-187">Zwracany jest błąd HTTP 404 (nie znaleziono).</span><span class="sxs-lookup"><span data-stu-id="5b19c-187">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="5b19c-188">Dołącz `/WeatherForecast` do adresu URL (Zmień adres URL na `https://localhost:<port>/WeatherForecast` ).</span><span class="sxs-lookup"><span data-stu-id="5b19c-188">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="5b19c-189">Zwracany jest kod JSON podobny do następującego:</span><span class="sxs-lookup"><span data-stu-id="5b19c-189">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="5b19c-190">Dodaj klasę modelu</span><span class="sxs-lookup"><span data-stu-id="5b19c-190">Add a model class</span></span>

<span data-ttu-id="5b19c-191">*Model* to zestaw klas, które reprezentują dane zarządzane przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="5b19c-191">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="5b19c-192">Model tej aplikacji jest pojedynczą `TodoItem` klasą.</span><span class="sxs-lookup"><span data-stu-id="5b19c-192">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5b19c-193">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5b19c-193">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5b19c-194">W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt.</span><span class="sxs-lookup"><span data-stu-id="5b19c-194">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="5b19c-195">Wybierz pozycję **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-195">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="5b19c-196">Nazwij *modele*folderów.</span><span class="sxs-lookup"><span data-stu-id="5b19c-196">Name the folder *Models*.</span></span>

* <span data-ttu-id="5b19c-197">Kliknij prawym przyciskiem myszy folder *modele* i wybierz polecenie **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-197">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="5b19c-198">Nadaj klasie nazwę *TodoItem* i wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-198">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="5b19c-199">Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="5b19c-199">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5b19c-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5b19c-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5b19c-201">Dodaj folder o nazwie *models*.</span><span class="sxs-lookup"><span data-stu-id="5b19c-201">Add a folder named *Models*.</span></span>

* <span data-ttu-id="5b19c-202">Dodaj `TodoItem` klasę do folderu *models* o następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="5b19c-202">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5b19c-203">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="5b19c-203">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="5b19c-204">Kliknij prawym przyciskiem myszy projekt.</span><span class="sxs-lookup"><span data-stu-id="5b19c-204">Right-click the project.</span></span> <span data-ttu-id="5b19c-205">Wybierz pozycję **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-205">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="5b19c-206">Nazwij *modele*folderów.</span><span class="sxs-lookup"><span data-stu-id="5b19c-206">Name the folder *Models*.</span></span>

  ![Nowy folder](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="5b19c-208">Kliknij prawym przyciskiem myszy folder *modele* , a następnie wybierz pozycję **Dodaj** > **nowy plik** > **ogólna** > **pusta Klasa**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-208">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="5b19c-209">Nazwij klasę *TodoItem*, a następnie kliknij pozycję **New (nowy**).</span><span class="sxs-lookup"><span data-stu-id="5b19c-209">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="5b19c-210">Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="5b19c-210">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="5b19c-211">`Id`Właściwość działa jako unikatowy klucz w relacyjnej bazie danych.</span><span class="sxs-lookup"><span data-stu-id="5b19c-211">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="5b19c-212">Klasy modelu mogą przejść do dowolnego miejsca w projekcie, ale folder *modele* jest używany przez Konwencję.</span><span class="sxs-lookup"><span data-stu-id="5b19c-212">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="5b19c-213">Dodawanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="5b19c-213">Add a database context</span></span>

<span data-ttu-id="5b19c-214">*Kontekst bazy danych* jest główną klasą, która koordynuje Entity Framework funkcji dla modelu danych.</span><span class="sxs-lookup"><span data-stu-id="5b19c-214">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="5b19c-215">Ta klasa jest tworzona przez wyprowadzanie z `Microsoft.EntityFrameworkCore.DbContext` klasy.</span><span class="sxs-lookup"><span data-stu-id="5b19c-215">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5b19c-216">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5b19c-216">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="5b19c-217">Dodaj Microsoft. EntityFrameworkCore. SqlServer</span><span class="sxs-lookup"><span data-stu-id="5b19c-217">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="5b19c-218">W menu **Narzędzia** wybierz pozycję **menedżer pakietów NuGet > zarządzanie pakietami NuGet dla rozwiązania**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-218">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="5b19c-219">Wybierz kartę **Przeglądaj** , a następnie w polu wyszukiwania wprowadź ciąg **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="5b19c-219">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="5b19c-220">W lewym okienku wybierz pozycję **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="5b19c-220">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="5b19c-221">Zaznacz pole wyboru **projekt** w prawym okienku, a następnie wybierz pozycję **Zainstaluj**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-221">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="5b19c-222">Aby dodać pakiet NuGet, użyj powyższych instrukcji `Microsoft.EntityFrameworkCore.InMemory` .</span><span class="sxs-lookup"><span data-stu-id="5b19c-222">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![Menedżer pakietów NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="5b19c-224">Dodawanie kontekstu bazy danych TodoContext</span><span class="sxs-lookup"><span data-stu-id="5b19c-224">Add the TodoContext database context</span></span>

* <span data-ttu-id="5b19c-225">Kliknij prawym przyciskiem myszy folder *modele* i wybierz polecenie **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-225">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="5b19c-226">Nadaj klasie nazwę *TodoContext* i kliknij przycisk **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-226">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="5b19c-227">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="5b19c-227">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="5b19c-228">Dodaj `TodoContext` klasę do folderu *models* .</span><span class="sxs-lookup"><span data-stu-id="5b19c-228">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="5b19c-229">Wprowadź następujący kod:</span><span class="sxs-lookup"><span data-stu-id="5b19c-229">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="5b19c-230">Rejestrowanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="5b19c-230">Register the database context</span></span>

<span data-ttu-id="5b19c-231">W ASP.NET Core usługi, takie jak kontekst bazy danych, muszą być zarejestrowane z kontenerem [iniekcji zależności (di)](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="5b19c-231">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="5b19c-232">Kontener udostępnia usługę kontrolerom.</span><span class="sxs-lookup"><span data-stu-id="5b19c-232">The container provides the service to controllers.</span></span>

<span data-ttu-id="5b19c-233">Zaktualizuj *Startup.cs* o następujący wyróżniony kod:</span><span class="sxs-lookup"><span data-stu-id="5b19c-233">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="5b19c-234">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="5b19c-234">The preceding code:</span></span>

* <span data-ttu-id="5b19c-235">Usuwa nieużywane `using` deklaracje.</span><span class="sxs-lookup"><span data-stu-id="5b19c-235">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="5b19c-236">Dodaje kontekst bazy danych do kontenera DI.</span><span class="sxs-lookup"><span data-stu-id="5b19c-236">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="5b19c-237">Określa, że kontekst bazy danych będzie używać bazy danych w pamięci.</span><span class="sxs-lookup"><span data-stu-id="5b19c-237">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="5b19c-238">Tworzenie szkieletu kontrolera</span><span class="sxs-lookup"><span data-stu-id="5b19c-238">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5b19c-239">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5b19c-239">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5b19c-240">Kliknij prawym przyciskiem myszy folder *controllers* .</span><span class="sxs-lookup"><span data-stu-id="5b19c-240">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="5b19c-241">Wybierz pozycję **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-241">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="5b19c-242">Wybierz pozycję **kontroler interfejsu API z akcjami, używając Entity Framework**, a następnie wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-242">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="5b19c-243">Na stronie **Dodawanie kontrolera interfejsu API z akcjami przy użyciu Entity Framework** dialogowego:</span><span class="sxs-lookup"><span data-stu-id="5b19c-243">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="5b19c-244">Wybierz pozycję **TodoItem (TodoApi. models)** w **klasie model**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-244">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="5b19c-245">W **klasie kontekstu danych**wybierz pozycję **TodoContext (TodoApi. models)** .</span><span class="sxs-lookup"><span data-stu-id="5b19c-245">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="5b19c-246">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-246">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="5b19c-247">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="5b19c-247">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="5b19c-248">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="5b19c-248">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="5b19c-249">Poprzednie polecenia:</span><span class="sxs-lookup"><span data-stu-id="5b19c-249">The preceding commands:</span></span>

* <span data-ttu-id="5b19c-250">Dodaj pakiety NuGet wymagane do tworzenia szkieletów.</span><span class="sxs-lookup"><span data-stu-id="5b19c-250">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="5b19c-251">Instaluje aparat tworzenia szkieletu ( `dotnet-aspnet-codegenerator` ).</span><span class="sxs-lookup"><span data-stu-id="5b19c-251">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="5b19c-252">Szkielety `TodoItemsController` .</span><span class="sxs-lookup"><span data-stu-id="5b19c-252">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="5b19c-253">Wygenerowany kod:</span><span class="sxs-lookup"><span data-stu-id="5b19c-253">The generated code:</span></span>

* <span data-ttu-id="5b19c-254">Oznacza klasę [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) atrybutem.</span><span class="sxs-lookup"><span data-stu-id="5b19c-254">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="5b19c-255">Ten atrybut wskazuje, że kontroler odpowiada na żądania interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="5b19c-255">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="5b19c-256">Aby uzyskać informacje o określonych zachowaniach, które włącza atrybut, zobacz <xref:web-api/index> .</span><span class="sxs-lookup"><span data-stu-id="5b19c-256">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="5b19c-257">Używa funkcji DI do iniekcji kontekstu bazy danych ( `TodoContext` ) do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="5b19c-257">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="5b19c-258">Kontekst bazy danych jest używany w każdej z metod [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) w kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="5b19c-258">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="5b19c-259">Szablony ASP.NET Core dla:</span><span class="sxs-lookup"><span data-stu-id="5b19c-259">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="5b19c-260">Kontrolery z widokami obejmują `[action]` szablon trasy.</span><span class="sxs-lookup"><span data-stu-id="5b19c-260">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="5b19c-261">Kontrolery interfejsu API nie należą `[action]` do szablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="5b19c-261">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="5b19c-262">Gdy `[action]` token nie znajduje się w szablonie trasy, nazwa [akcji](xref:mvc/controllers/routing#action) jest wykluczona z trasy.</span><span class="sxs-lookup"><span data-stu-id="5b19c-262">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="5b19c-263">Oznacza to, że nazwa metody skojarzonej z akcją nie jest używana w zgodnej trasie.</span><span class="sxs-lookup"><span data-stu-id="5b19c-263">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="5b19c-264">Badanie metody PostTodoItem Create</span><span class="sxs-lookup"><span data-stu-id="5b19c-264">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="5b19c-265">Zastąp instrukcję return w, `PostTodoItem` Aby użyć operatora [nameof](/dotnet/csharp/language-reference/operators/nameof) :</span><span class="sxs-lookup"><span data-stu-id="5b19c-265">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="5b19c-266">Poprzedni kod jest metodą POST protokołu HTTP, jak wskazano w [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) atrybucie.</span><span class="sxs-lookup"><span data-stu-id="5b19c-266">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="5b19c-267">Metoda pobiera wartość elementu do wykonania z treści żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="5b19c-267">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="5b19c-268"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>Metoda:</span><span class="sxs-lookup"><span data-stu-id="5b19c-268">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="5b19c-269">W razie powodzenia zwraca kod stanu HTTP 201.</span><span class="sxs-lookup"><span data-stu-id="5b19c-269">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="5b19c-270">HTTP 201 to standardowa odpowiedź dla metody POST protokołu HTTP, która tworzy nowy zasób na serwerze.</span><span class="sxs-lookup"><span data-stu-id="5b19c-270">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="5b19c-271">Dodaje nagłówek [lokalizacji](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) do odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="5b19c-271">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="5b19c-272">`Location`Nagłówek określa [Identyfikator URI](https://developer.mozilla.org/docs/Glossary/URI) nowo utworzonego elementu do wykonania.</span><span class="sxs-lookup"><span data-stu-id="5b19c-272">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="5b19c-273">Aby uzyskać więcej informacji, zobacz [10.2.2 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="5b19c-273">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="5b19c-274">Odwołuje się do `GetTodoItem` akcji tworzenia `Location` identyfikatora URI nagłówka.</span><span class="sxs-lookup"><span data-stu-id="5b19c-274">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="5b19c-275">`nameof`Słowo kluczowe języka C# służy do zapobiegania twardemu kodowaniu nazwy akcji w `CreatedAtAction` wywołaniu.</span><span class="sxs-lookup"><span data-stu-id="5b19c-275">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="5b19c-276">Zainstaluj program Poster</span><span class="sxs-lookup"><span data-stu-id="5b19c-276">Install Postman</span></span>

<span data-ttu-id="5b19c-277">Ten samouczek używa programu do testowania interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="5b19c-277">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="5b19c-278">Zainstaluj program [Poster](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="5b19c-278">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="5b19c-279">Uruchom aplikację internetową.</span><span class="sxs-lookup"><span data-stu-id="5b19c-279">Start the web app.</span></span>
* <span data-ttu-id="5b19c-280">Uruchom wpis.</span><span class="sxs-lookup"><span data-stu-id="5b19c-280">Start Postman.</span></span>
* <span data-ttu-id="5b19c-281">Wyłącz **weryfikację certyfikatu SSL**</span><span class="sxs-lookup"><span data-stu-id="5b19c-281">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="5b19c-282">W **File** obszarze > **Ustawienia** pliku (karta**Ogólne** ) Wyłącz **weryfikację certyfikatu SSL**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-282">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="5b19c-283">Po przetestowaniu kontrolera ponownie Włącz weryfikację certyfikatu SSL.</span><span class="sxs-lookup"><span data-stu-id="5b19c-283">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="5b19c-284">Test PostTodoItem za pomocą programu Poster</span><span class="sxs-lookup"><span data-stu-id="5b19c-284">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="5b19c-285">Utwórz nowe żądanie.</span><span class="sxs-lookup"><span data-stu-id="5b19c-285">Create a new request.</span></span>
* <span data-ttu-id="5b19c-286">Ustaw metodę HTTP na `POST` .</span><span class="sxs-lookup"><span data-stu-id="5b19c-286">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="5b19c-287">Wybierz kartę **Treść**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-287">Select the **Body** tab.</span></span>
* <span data-ttu-id="5b19c-288">Wybierz przycisk radiowy **RAW** .</span><span class="sxs-lookup"><span data-stu-id="5b19c-288">Select the **raw** radio button.</span></span>
* <span data-ttu-id="5b19c-289">Ustaw typ na **JSON (Application/JSON)**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-289">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="5b19c-290">W treści żądania wprowadź kod JSON dla elementu do wykonania:</span><span class="sxs-lookup"><span data-stu-id="5b19c-290">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="5b19c-291">Wybierz pozycję **Send** (Wyślij).</span><span class="sxs-lookup"><span data-stu-id="5b19c-291">Select **Send**.</span></span>

  ![Ogłoś przy użyciu żądania Create](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="5b19c-293">Testowanie identyfikatora URI nagłówka lokalizacji</span><span class="sxs-lookup"><span data-stu-id="5b19c-293">Test the location header URI</span></span>

* <span data-ttu-id="5b19c-294">Wybierz kartę **nagłówki** w okienku **odpowiedź** .</span><span class="sxs-lookup"><span data-stu-id="5b19c-294">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="5b19c-295">Skopiuj wartość nagłówka **lokalizacji** :</span><span class="sxs-lookup"><span data-stu-id="5b19c-295">Copy the **Location** header value:</span></span>

  ![Karta nagłówki w konsoli programu Poster](first-web-api/_static/3/create.png)

* <span data-ttu-id="5b19c-297">Ustaw metodę, aby uzyskać.</span><span class="sxs-lookup"><span data-stu-id="5b19c-297">Set the method to GET.</span></span>
* <span data-ttu-id="5b19c-298">Wklej URI (na przykład `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="5b19c-298">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="5b19c-299">Wybierz pozycję **Send** (Wyślij).</span><span class="sxs-lookup"><span data-stu-id="5b19c-299">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="5b19c-300">Badanie metod GET</span><span class="sxs-lookup"><span data-stu-id="5b19c-300">Examine the GET methods</span></span>

<span data-ttu-id="5b19c-301">Te metody implementują dwa punkty końcowe GET:</span><span class="sxs-lookup"><span data-stu-id="5b19c-301">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="5b19c-302">Przetestuj aplikację, wywołując dwa punkty końcowe z przeglądarki lub wpisu.</span><span class="sxs-lookup"><span data-stu-id="5b19c-302">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="5b19c-303">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="5b19c-303">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="5b19c-304">Odpowiedź podobna do poniższego jest generowana przez wywołanie `GetTodoItems` :</span><span class="sxs-lookup"><span data-stu-id="5b19c-304">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="5b19c-305">Test get przy użyciu programu Poster</span><span class="sxs-lookup"><span data-stu-id="5b19c-305">Test Get with Postman</span></span>

* <span data-ttu-id="5b19c-306">Utwórz nowe żądanie.</span><span class="sxs-lookup"><span data-stu-id="5b19c-306">Create a new request.</span></span>
* <span data-ttu-id="5b19c-307">Ustaw metodę HTTP, aby **uzyskać**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-307">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="5b19c-308">Ustaw adres URL żądania `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="5b19c-308">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="5b19c-309">Na przykład `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="5b19c-309">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="5b19c-310">Ustaw **dwa widoki okienka** w programie Poster.</span><span class="sxs-lookup"><span data-stu-id="5b19c-310">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="5b19c-311">Wybierz pozycję **Send** (Wyślij).</span><span class="sxs-lookup"><span data-stu-id="5b19c-311">Select **Send**.</span></span>

<span data-ttu-id="5b19c-312">Ta aplikacja używa bazy danych w pamięci.</span><span class="sxs-lookup"><span data-stu-id="5b19c-312">This app uses an in-memory database.</span></span> <span data-ttu-id="5b19c-313">Jeśli aplikacja zostanie zatrzymana i uruchomiona, poprzednie żądanie GET nie zwróci żadnych danych.</span><span class="sxs-lookup"><span data-stu-id="5b19c-313">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="5b19c-314">Jeśli nie zostaną zwrócone żadne dane, [Opublikuj](#post) dane w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5b19c-314">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="5b19c-315">Ścieżki routingu i adresów URL</span><span class="sxs-lookup"><span data-stu-id="5b19c-315">Routing and URL paths</span></span>

<span data-ttu-id="5b19c-316">Ten [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) atrybut oznacza metodę, która reaguje na żądanie HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="5b19c-316">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="5b19c-317">Ścieżka adresu URL dla każdej metody jest zbudowana w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="5b19c-317">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="5b19c-318">Zacznij od ciągu szablonu w `Route` atrybucie kontrolera:</span><span class="sxs-lookup"><span data-stu-id="5b19c-318">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="5b19c-319">Zastąp `[controller]` nazwą kontrolera, którą Konwencją jest nazwa klasy kontrolera minus sufiks "Controller".</span><span class="sxs-lookup"><span data-stu-id="5b19c-319">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="5b19c-320">Dla tego przykładu nazwa klasy kontrolera to **TodoItems**Controller, więc nazwa kontrolera to "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="5b19c-320">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="5b19c-321">W ASP.NET Core [routingu](xref:mvc/controllers/routing) jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="5b19c-321">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="5b19c-322">Jeśli `[HttpGet]` atrybut ma szablon trasy (na przykład `[HttpGet("products")]` ), Dodaj go do ścieżki.</span><span class="sxs-lookup"><span data-stu-id="5b19c-322">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="5b19c-323">Ten przykład nie używa szablonu.</span><span class="sxs-lookup"><span data-stu-id="5b19c-323">This sample doesn't use a template.</span></span> <span data-ttu-id="5b19c-324">Aby uzyskać więcej informacji, zobacz temat [Routing atrybutów z atrybutami http [Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="5b19c-324">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="5b19c-325">W poniższej `GetTodoItem` metodzie `"{id}"` jest zmienną zastępczą dla unikatowego identyfikatora elementu do wykonania.</span><span class="sxs-lookup"><span data-stu-id="5b19c-325">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="5b19c-326">Gdy `GetTodoItem` jest wywoływana, wartość `"{id}"` w adresie URL jest podawana do metody w jej `id` parametrze.</span><span class="sxs-lookup"><span data-stu-id="5b19c-326">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="5b19c-327">Zwracane wartości</span><span class="sxs-lookup"><span data-stu-id="5b19c-327">Return values</span></span>

<span data-ttu-id="5b19c-328">Zwracany typ `GetTodoItems` `GetTodoItem` metod i jest [ \<T> typem ActionResult](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="5b19c-328">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="5b19c-329">ASP.NET Core automatycznie serializować obiektu do [formatu JSON](https://www.json.org/) i zapisuje kod JSON w treści komunikatu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="5b19c-329">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="5b19c-330">Kod odpowiedzi dla tego typu zwracanego to 200, przy założeniu, że nie istnieją Nieobsłużone wyjątki.</span><span class="sxs-lookup"><span data-stu-id="5b19c-330">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="5b19c-331">Nieobsłużone wyjątki są tłumaczone na błędy 5xx.</span><span class="sxs-lookup"><span data-stu-id="5b19c-331">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="5b19c-332">`ActionResult`typy zwracane mogą reprezentować szeroką gamę kodów stanu HTTP.</span><span class="sxs-lookup"><span data-stu-id="5b19c-332">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="5b19c-333">Na przykład `GetTodoItem` może zwracać dwie różne wartości stanu:</span><span class="sxs-lookup"><span data-stu-id="5b19c-333">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="5b19c-334">Jeśli żaden element nie jest zgodny z żądanym IDENTYFIKATORem, metoda zwraca 404 kod błędu [NOTFOUND](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) .</span><span class="sxs-lookup"><span data-stu-id="5b19c-334">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="5b19c-335">W przeciwnym razie metoda zwraca 200 z treścią odpowiedzi JSON.</span><span class="sxs-lookup"><span data-stu-id="5b19c-335">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="5b19c-336">Zwracanie `item` wyników w odpowiedzi HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="5b19c-336">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="5b19c-337">Metoda PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="5b19c-337">The PutTodoItem method</span></span>

<span data-ttu-id="5b19c-338">Badanie `PutTodoItem` metody:</span><span class="sxs-lookup"><span data-stu-id="5b19c-338">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="5b19c-339">`PutTodoItem`jest podobny do `PostTodoItem` , z tą różnicą, że używa protokołu HTTP Put.</span><span class="sxs-lookup"><span data-stu-id="5b19c-339">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="5b19c-340">Odpowiedź to [204 (brak zawartości)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="5b19c-340">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="5b19c-341">Zgodnie ze specyfikacją protokołu HTTP żądanie PUT wymaga, aby klient wysłał całą zaktualizowaną jednostkę, a nie tylko te zmiany.</span><span class="sxs-lookup"><span data-stu-id="5b19c-341">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="5b19c-342">Aby zapewnić obsługę częściowych aktualizacji, użyj [poprawki http](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="5b19c-342">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="5b19c-343">Jeśli wystąpi błąd podczas wywoływania `PutTodoItem` , wywołaj, `GET` Aby upewnić się, że w bazie danych znajduje się element.</span><span class="sxs-lookup"><span data-stu-id="5b19c-343">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="5b19c-344">Testowanie metody PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="5b19c-344">Test the PutTodoItem method</span></span>

<span data-ttu-id="5b19c-345">Ten przykład korzysta z bazy danych w pamięci, która musi zostać zainicjowana za każdym razem, gdy aplikacja zostanie uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="5b19c-345">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="5b19c-346">Przed wykonaniem wywołania PUT musi istnieć element w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="5b19c-346">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="5b19c-347">Wywołaj polecenie GET, aby upewnić się, że w bazie danych znajduje się element, przed wykonaniem wywołania PUT.</span><span class="sxs-lookup"><span data-stu-id="5b19c-347">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="5b19c-348">Zaktualizuj element do wykonania o IDENTYFIKATORze 1 i ustaw jego nazwę na "Źródło danych":</span><span class="sxs-lookup"><span data-stu-id="5b19c-348">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="5b19c-349">Na poniższej ilustracji przedstawiono aktualizację programu Poster:</span><span class="sxs-lookup"><span data-stu-id="5b19c-349">The following image shows the Postman update:</span></span>

![Konsola programu Poster pokazująca odpowiedź 204 (brak zawartości)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="5b19c-351">Metoda DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="5b19c-351">The DeleteTodoItem method</span></span>

<span data-ttu-id="5b19c-352">Badanie `DeleteTodoItem` metody:</span><span class="sxs-lookup"><span data-stu-id="5b19c-352">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="5b19c-353">Testowanie metody DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="5b19c-353">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="5b19c-354">Użyj programu Poster, aby usunąć element do wykonania:</span><span class="sxs-lookup"><span data-stu-id="5b19c-354">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="5b19c-355">Ustaw metodę na `DELETE` .</span><span class="sxs-lookup"><span data-stu-id="5b19c-355">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="5b19c-356">Ustaw identyfikator URI obiektu do usunięcia (na przykład `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="5b19c-356">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="5b19c-357">Wybierz pozycję **Send** (Wyślij).</span><span class="sxs-lookup"><span data-stu-id="5b19c-357">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="5b19c-358">Zapobiegaj za pośrednictwem księgowania</span><span class="sxs-lookup"><span data-stu-id="5b19c-358">Prevent over-posting</span></span>

<span data-ttu-id="5b19c-359">Obecnie Przykładowa aplikacja uwidacznia cały `TodoItem` obiekt.</span><span class="sxs-lookup"><span data-stu-id="5b19c-359">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="5b19c-360">Aplikacje produkcyjne zwykle ograniczają dane wejściowe i zwracane przy użyciu podzestawu modelu.</span><span class="sxs-lookup"><span data-stu-id="5b19c-360">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="5b19c-361">Istnieje wiele powodów związanych z tym, a zabezpieczenia są głównymi.</span><span class="sxs-lookup"><span data-stu-id="5b19c-361">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="5b19c-362">Podzestaw modelu jest zwykle określany jako obiekt Transfer danych (DTO), model wejściowy lub model widoku.</span><span class="sxs-lookup"><span data-stu-id="5b19c-362">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="5b19c-363">**DTO** jest używany w tym artykule.</span><span class="sxs-lookup"><span data-stu-id="5b19c-363">**DTO** is used in this article.</span></span>

<span data-ttu-id="5b19c-364">DTO może służyć do:</span><span class="sxs-lookup"><span data-stu-id="5b19c-364">A DTO may be used to:</span></span>

* <span data-ttu-id="5b19c-365">Zablokuj nadmierne księgowanie.</span><span class="sxs-lookup"><span data-stu-id="5b19c-365">Prevent over-posting.</span></span>
* <span data-ttu-id="5b19c-366">Ukryj właściwości, które nie powinny być wyświetlane dla klientów.</span><span class="sxs-lookup"><span data-stu-id="5b19c-366">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="5b19c-367">Pomiń niektóre właściwości, aby zmniejszyć rozmiar ładunku.</span><span class="sxs-lookup"><span data-stu-id="5b19c-367">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="5b19c-368">Spłaszcz wykresy obiektów zawierające obiekty zagnieżdżone.</span><span class="sxs-lookup"><span data-stu-id="5b19c-368">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="5b19c-369">Spłaszczone wykresy obiektów mogą być wygodniejsze dla klientów.</span><span class="sxs-lookup"><span data-stu-id="5b19c-369">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="5b19c-370">Aby zademonstrować podejście DTO, zaktualizuj `TodoItem` klasę w celu uwzględnienia pola tajnego:</span><span class="sxs-lookup"><span data-stu-id="5b19c-370">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="5b19c-371">Pole tajne musi być ukryte w tej aplikacji, ale aplikacja administracyjna mogła ją uwidocznić.</span><span class="sxs-lookup"><span data-stu-id="5b19c-371">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="5b19c-372">Sprawdź, czy można opublikować i pobrać pole tajne.</span><span class="sxs-lookup"><span data-stu-id="5b19c-372">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="5b19c-373">Utwórz model DTO:</span><span class="sxs-lookup"><span data-stu-id="5b19c-373">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="5b19c-374">Zaktualizuj, `TodoItemsController` Aby użyć `TodoItemDTO` :</span><span class="sxs-lookup"><span data-stu-id="5b19c-374">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="5b19c-375">Upewnij się, że nie można opublikować lub pobrać pola tajnego.</span><span class="sxs-lookup"><span data-stu-id="5b19c-375">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="5b19c-376">Wywoływanie interfejsu API sieci Web przy użyciu języka JavaScript</span><span class="sxs-lookup"><span data-stu-id="5b19c-376">Call the web API with JavaScript</span></span>

<span data-ttu-id="5b19c-377">Zobacz [Samouczek: wywoływanie interfejsu API sieci web ASP.NET Core przy użyciu języka JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="5b19c-377">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5b19c-378">Ten samouczek zawiera informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="5b19c-378">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5b19c-379">Utwórz projekt interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="5b19c-379">Create a web API project.</span></span>
> * <span data-ttu-id="5b19c-380">Dodaj klasę modelu i kontekst bazy danych.</span><span class="sxs-lookup"><span data-stu-id="5b19c-380">Add a model class and a database context.</span></span>
> * <span data-ttu-id="5b19c-381">Dodaj kontroler.</span><span class="sxs-lookup"><span data-stu-id="5b19c-381">Add a controller.</span></span>
> * <span data-ttu-id="5b19c-382">Dodaj metody CRUD.</span><span class="sxs-lookup"><span data-stu-id="5b19c-382">Add CRUD methods.</span></span>
> * <span data-ttu-id="5b19c-383">Skonfiguruj ścieżki routingu i adresów URL.</span><span class="sxs-lookup"><span data-stu-id="5b19c-383">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="5b19c-384">Określ wartości zwracane.</span><span class="sxs-lookup"><span data-stu-id="5b19c-384">Specify return values.</span></span>
> * <span data-ttu-id="5b19c-385">Wywołaj interfejs API sieci Web za pomocą programu Poster.</span><span class="sxs-lookup"><span data-stu-id="5b19c-385">Call the web API with Postman.</span></span>
> * <span data-ttu-id="5b19c-386">Wywołaj interfejs API sieci Web za pomocą języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5b19c-386">Call the web API with JavaScript.</span></span>

<span data-ttu-id="5b19c-387">Na końcu znajduje się internetowy interfejs API, który może zarządzać elementami do wykonania przechowywanymi w relacyjnej bazie danych.</span><span class="sxs-lookup"><span data-stu-id="5b19c-387">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="5b19c-388">Omówienie</span><span class="sxs-lookup"><span data-stu-id="5b19c-388">Overview</span></span>

<span data-ttu-id="5b19c-389">Ten samouczek tworzy następujący interfejs API:</span><span class="sxs-lookup"><span data-stu-id="5b19c-389">This tutorial creates the following API:</span></span>

|<span data-ttu-id="5b19c-390">Interfejs API</span><span class="sxs-lookup"><span data-stu-id="5b19c-390">API</span></span> | <span data-ttu-id="5b19c-391">Opis</span><span class="sxs-lookup"><span data-stu-id="5b19c-391">Description</span></span> | <span data-ttu-id="5b19c-392">Treść żądania</span><span class="sxs-lookup"><span data-stu-id="5b19c-392">Request body</span></span> | <span data-ttu-id="5b19c-393">Treść odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="5b19c-393">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="5b19c-394">Pobierz/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="5b19c-394">GET /api/TodoItems</span></span> | <span data-ttu-id="5b19c-395">Pobierz wszystkie elementy do wykonania</span><span class="sxs-lookup"><span data-stu-id="5b19c-395">Get all to-do items</span></span> | <span data-ttu-id="5b19c-396">Brak</span><span class="sxs-lookup"><span data-stu-id="5b19c-396">None</span></span> | <span data-ttu-id="5b19c-397">Tablica elementów do wykonania</span><span class="sxs-lookup"><span data-stu-id="5b19c-397">Array of to-do items</span></span>|
|<span data-ttu-id="5b19c-398">Pobierz/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="5b19c-398">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="5b19c-399">Pobieranie elementu według identyfikatora</span><span class="sxs-lookup"><span data-stu-id="5b19c-399">Get an item by ID</span></span> | <span data-ttu-id="5b19c-400">Brak</span><span class="sxs-lookup"><span data-stu-id="5b19c-400">None</span></span> | <span data-ttu-id="5b19c-401">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="5b19c-401">To-do item</span></span>|
|<span data-ttu-id="5b19c-402">Opublikuj/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="5b19c-402">POST /api/TodoItems</span></span> | <span data-ttu-id="5b19c-403">Dodaj nowy element</span><span class="sxs-lookup"><span data-stu-id="5b19c-403">Add a new item</span></span> | <span data-ttu-id="5b19c-404">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="5b19c-404">To-do item</span></span> | <span data-ttu-id="5b19c-405">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="5b19c-405">To-do item</span></span> |
|<span data-ttu-id="5b19c-406">Umieść/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="5b19c-406">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="5b19c-407">Aktualizowanie istniejącego elementu&nbsp;</span><span class="sxs-lookup"><span data-stu-id="5b19c-407">Update an existing item &nbsp;</span></span> | <span data-ttu-id="5b19c-408">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="5b19c-408">To-do item</span></span> | <span data-ttu-id="5b19c-409">Brak</span><span class="sxs-lookup"><span data-stu-id="5b19c-409">None</span></span> |
|<span data-ttu-id="5b19c-410">Usuń/api/TodoItems/{id} &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="5b19c-410">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="5b19c-411">Usuń element &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="5b19c-411">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="5b19c-412">Brak</span><span class="sxs-lookup"><span data-stu-id="5b19c-412">None</span></span> | <span data-ttu-id="5b19c-413">Brak</span><span class="sxs-lookup"><span data-stu-id="5b19c-413">None</span></span>|

<span data-ttu-id="5b19c-414">Na poniższym diagramie przedstawiono projekt aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5b19c-414">The following diagram shows the design of the app.</span></span>

![Klient jest reprezentowany przez pole po lewej stronie.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="5b19c-420">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="5b19c-420">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5b19c-421">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5b19c-421">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="5b19c-422">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5b19c-422">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5b19c-423">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="5b19c-423">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="5b19c-424">Tworzenie projektu sieci Web</span><span class="sxs-lookup"><span data-stu-id="5b19c-424">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5b19c-425">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5b19c-425">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5b19c-426">Z menu **plik** wybierz pozycję **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-426">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="5b19c-427">Wybierz szablon **aplikacja sieci Web ASP.NET Core** a następnie kliknij przycisk **dalej**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-427">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="5b19c-428">Nazwij projekt *TodoApi* i kliknij pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-428">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="5b19c-429">W oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** upewnij się, że wybrano opcję **.net Core** i **ASP.NET Core 2,2** .</span><span class="sxs-lookup"><span data-stu-id="5b19c-429">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="5b19c-430">Wybierz szablon **interfejsu API** i kliknij przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-430">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="5b19c-431">**Nie** zaznaczaj opcji **Włącz obsługę platformy Docker**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-431">**Don't** select **Enable Docker Support**.</span></span>

![Okno dialogowe programu VS New Project](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="5b19c-433">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5b19c-433">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5b19c-434">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="5b19c-434">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="5b19c-435">Zmień katalog ( `cd` ) do folderu, który będzie zawierać folder projektu.</span><span class="sxs-lookup"><span data-stu-id="5b19c-435">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="5b19c-436">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="5b19c-436">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="5b19c-437">Te polecenia tworzą nowy projekt internetowego interfejsu API i otwierają nowe wystąpienie Visual Studio Code w nowym folderze projektu.</span><span class="sxs-lookup"><span data-stu-id="5b19c-437">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="5b19c-438">Gdy zostanie wyświetlone okno dialogowe z pytaniem, czy chcesz dodać wymagane zasoby do projektu, wybierz opcję **tak**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-438">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5b19c-439">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="5b19c-439">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="5b19c-440">Wybierz pozycję **plik** > **nowe rozwiązanie**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-440">Select **File** > **New Solution**.</span></span>

  ![macOS nowe rozwiązanie](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="5b19c-442">W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz **.NET Core**pozycję  >  **App**  >  **interfejs API**aplikacji .NET Core  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-442">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="5b19c-443">W wersji 8,6 lub nowszej wybierz pozycję **Web and Console**  >  **App**  >  **interfejs API**  >  **Next**aplikacji sieci Web i konsoli.</span><span class="sxs-lookup"><span data-stu-id="5b19c-443">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="5b19c-444">W oknie dialogowym **Konfigurowanie nowego interfejsu API sieci Web ASP.NET Core** zaakceptuj domyślną **platformę docelową** programu \**.NET Core 2,2*.</span><span class="sxs-lookup"><span data-stu-id="5b19c-444">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="5b19c-445">Wprowadź *TodoApi* jako **nazwę projektu** , a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-445">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![okno dialogowe konfiguracji](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="5b19c-447">Testowanie interfejsu API</span><span class="sxs-lookup"><span data-stu-id="5b19c-447">Test the API</span></span>

<span data-ttu-id="5b19c-448">Szablon projektu tworzy `values` interfejs API.</span><span class="sxs-lookup"><span data-stu-id="5b19c-448">The project template creates a `values` API.</span></span> <span data-ttu-id="5b19c-449">Wywołaj `Get` metodę z przeglądarki, aby przetestować aplikację.</span><span class="sxs-lookup"><span data-stu-id="5b19c-449">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5b19c-450">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5b19c-450">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5b19c-451">Naciśnij klawisze CTRL + F5, aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="5b19c-451">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="5b19c-452">Program Visual Studio uruchamia przeglądarkę i przechodzi do `https://localhost:<port>/api/values` lokalizacji, gdzie `<port>` jest losowo wybierany numer portu.</span><span class="sxs-lookup"><span data-stu-id="5b19c-452">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="5b19c-453">Jeśli zostanie wyświetlone okno dialogowe z pytaniem, czy należy zaufać certyfikatowi IIS Express, wybierz pozycję **tak**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-453">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="5b19c-454">W wyświetlonym oknie dialogowym **ostrzeżenia o zabezpieczeniach** wybierz pozycję **tak**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-454">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5b19c-455">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5b19c-455">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5b19c-456">Naciśnij klawisze CTRL + F5, aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="5b19c-456">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="5b19c-457">W przeglądarce przejdź do następującego adresu URL: `https://localhost:5001/api/values` .</span><span class="sxs-lookup"><span data-stu-id="5b19c-457">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5b19c-458">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="5b19c-458">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5b19c-459">Wybierz pozycję **Uruchom**  >  **Rozpocznij debugowanie** , aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="5b19c-459">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="5b19c-460">Visual Studio dla komputerów Mac uruchamia przeglądarkę i nawiguje do `https://localhost:<port>` , gdzie `<port>` jest losowo wybranym numerem portu.</span><span class="sxs-lookup"><span data-stu-id="5b19c-460">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="5b19c-461">Zwracany jest błąd HTTP 404 (nie znaleziono).</span><span class="sxs-lookup"><span data-stu-id="5b19c-461">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="5b19c-462">Dołącz `/api/values` do adresu URL (Zmień adres URL na `https://localhost:<port>/api/values` ).</span><span class="sxs-lookup"><span data-stu-id="5b19c-462">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="5b19c-463">Zostanie zwrócony następujący kod JSON:</span><span class="sxs-lookup"><span data-stu-id="5b19c-463">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="5b19c-464">Dodaj klasę modelu</span><span class="sxs-lookup"><span data-stu-id="5b19c-464">Add a model class</span></span>

<span data-ttu-id="5b19c-465">*Model* to zestaw klas, które reprezentują dane zarządzane przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="5b19c-465">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="5b19c-466">Model tej aplikacji jest pojedynczą `TodoItem` klasą.</span><span class="sxs-lookup"><span data-stu-id="5b19c-466">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5b19c-467">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5b19c-467">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5b19c-468">W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt.</span><span class="sxs-lookup"><span data-stu-id="5b19c-468">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="5b19c-469">Wybierz pozycję **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-469">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="5b19c-470">Nazwij *modele*folderów.</span><span class="sxs-lookup"><span data-stu-id="5b19c-470">Name the folder *Models*.</span></span>

* <span data-ttu-id="5b19c-471">Kliknij prawym przyciskiem myszy folder *modele* i wybierz polecenie **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-471">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="5b19c-472">Nadaj klasie nazwę *TodoItem* i wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-472">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="5b19c-473">Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="5b19c-473">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5b19c-474">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5b19c-474">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5b19c-475">Dodaj folder o nazwie *models*.</span><span class="sxs-lookup"><span data-stu-id="5b19c-475">Add a folder named *Models*.</span></span>

* <span data-ttu-id="5b19c-476">Dodaj `TodoItem` klasę do folderu *models* o następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="5b19c-476">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5b19c-477">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="5b19c-477">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="5b19c-478">Kliknij prawym przyciskiem myszy projekt.</span><span class="sxs-lookup"><span data-stu-id="5b19c-478">Right-click the project.</span></span> <span data-ttu-id="5b19c-479">Wybierz pozycję **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-479">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="5b19c-480">Nazwij *modele*folderów.</span><span class="sxs-lookup"><span data-stu-id="5b19c-480">Name the folder *Models*.</span></span>

  ![Nowy folder](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="5b19c-482">Kliknij prawym przyciskiem myszy folder *modele* , a następnie wybierz pozycję **Dodaj** > **nowy plik** > **ogólna** > **pusta Klasa**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-482">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="5b19c-483">Nazwij klasę *TodoItem*, a następnie kliknij pozycję **New (nowy**).</span><span class="sxs-lookup"><span data-stu-id="5b19c-483">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="5b19c-484">Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="5b19c-484">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="5b19c-485">`Id`Właściwość działa jako unikatowy klucz w relacyjnej bazie danych.</span><span class="sxs-lookup"><span data-stu-id="5b19c-485">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="5b19c-486">Klasy modelu mogą przejść do dowolnego miejsca w projekcie, ale folder *modele* jest używany przez Konwencję.</span><span class="sxs-lookup"><span data-stu-id="5b19c-486">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="5b19c-487">Dodawanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="5b19c-487">Add a database context</span></span>

<span data-ttu-id="5b19c-488">*Kontekst bazy danych* jest główną klasą, która koordynuje Entity Framework funkcji dla modelu danych.</span><span class="sxs-lookup"><span data-stu-id="5b19c-488">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="5b19c-489">Ta klasa jest tworzona przez wyprowadzanie z `Microsoft.EntityFrameworkCore.DbContext` klasy.</span><span class="sxs-lookup"><span data-stu-id="5b19c-489">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5b19c-490">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5b19c-490">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5b19c-491">Kliknij prawym przyciskiem myszy folder *modele* i wybierz polecenie **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-491">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="5b19c-492">Nadaj klasie nazwę *TodoContext* i kliknij przycisk **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-492">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="5b19c-493">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="5b19c-493">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="5b19c-494">Dodaj `TodoContext` klasę do folderu *models* .</span><span class="sxs-lookup"><span data-stu-id="5b19c-494">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="5b19c-495">Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="5b19c-495">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="5b19c-496">Rejestrowanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="5b19c-496">Register the database context</span></span>

<span data-ttu-id="5b19c-497">W ASP.NET Core usługi, takie jak kontekst bazy danych, muszą być zarejestrowane z kontenerem [iniekcji zależności (di)](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="5b19c-497">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="5b19c-498">Kontener udostępnia usługę kontrolerom.</span><span class="sxs-lookup"><span data-stu-id="5b19c-498">The container provides the service to controllers.</span></span>

<span data-ttu-id="5b19c-499">Zaktualizuj *Startup.cs* o następujący wyróżniony kod:</span><span class="sxs-lookup"><span data-stu-id="5b19c-499">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="5b19c-500">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="5b19c-500">The preceding code:</span></span>

* <span data-ttu-id="5b19c-501">Usuwa nieużywane `using` deklaracje.</span><span class="sxs-lookup"><span data-stu-id="5b19c-501">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="5b19c-502">Dodaje kontekst bazy danych do kontenera DI.</span><span class="sxs-lookup"><span data-stu-id="5b19c-502">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="5b19c-503">Określa, że kontekst bazy danych będzie używać bazy danych w pamięci.</span><span class="sxs-lookup"><span data-stu-id="5b19c-503">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="5b19c-504">Dodawanie kontrolera</span><span class="sxs-lookup"><span data-stu-id="5b19c-504">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5b19c-505">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5b19c-505">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5b19c-506">Kliknij prawym przyciskiem myszy folder *controllers* .</span><span class="sxs-lookup"><span data-stu-id="5b19c-506">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="5b19c-507">Wybierz pozycję **Dodaj** > **nowy element**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-507">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="5b19c-508">W oknie dialogowym **Dodaj nowy element** wybierz szablon **Klasa kontrolera interfejsu API** .</span><span class="sxs-lookup"><span data-stu-id="5b19c-508">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="5b19c-509">Nadaj klasie nazwę *TodoController*i wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-509">Name the class *TodoController*, and select **Add**.</span></span>

  ![Okno dialogowe Dodawanie nowego elementu z kontrolerem w polu wyszukiwania i wybranym kontrolerem interfejsu API sieci Web](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="5b19c-511">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="5b19c-511">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="5b19c-512">W folderze *controllers* Utwórz klasę o nazwie `TodoController` .</span><span class="sxs-lookup"><span data-stu-id="5b19c-512">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="5b19c-513">Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="5b19c-513">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="5b19c-514">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="5b19c-514">The preceding code:</span></span>

* <span data-ttu-id="5b19c-515">Definiuje klasę kontrolera interfejsu API bez metod.</span><span class="sxs-lookup"><span data-stu-id="5b19c-515">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="5b19c-516">Oznacza klasę [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) atrybutem.</span><span class="sxs-lookup"><span data-stu-id="5b19c-516">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="5b19c-517">Ten atrybut wskazuje, że kontroler odpowiada na żądania interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="5b19c-517">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="5b19c-518">Aby uzyskać informacje o określonych zachowaniach, które włącza atrybut, zobacz <xref:web-api/index> .</span><span class="sxs-lookup"><span data-stu-id="5b19c-518">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="5b19c-519">Używa funkcji DI do iniekcji kontekstu bazy danych ( `TodoContext` ) do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="5b19c-519">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="5b19c-520">Kontekst bazy danych jest używany w każdej z metod [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) w kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="5b19c-520">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="5b19c-521">Dodaje element o nazwie `Item1` do bazy danych, jeśli baza danych jest pusta.</span><span class="sxs-lookup"><span data-stu-id="5b19c-521">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="5b19c-522">Ten kod znajduje się w konstruktorze, więc jest uruchamiany za każdym razem, gdy istnieje nowe żądanie HTTP.</span><span class="sxs-lookup"><span data-stu-id="5b19c-522">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="5b19c-523">Jeśli usuniesz wszystkie elementy, Konstruktor zostanie ponownie utworzony `Item1` przy następnym wywołaniu metody interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="5b19c-523">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="5b19c-524">Dlatego może wyglądać podobnie jak usunięcie nie zadziałało, gdy rzeczywiście zadziałało.</span><span class="sxs-lookup"><span data-stu-id="5b19c-524">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="5b19c-525">Dodawanie metod get</span><span class="sxs-lookup"><span data-stu-id="5b19c-525">Add Get methods</span></span>

<span data-ttu-id="5b19c-526">Aby udostępnić interfejs API, który pobiera elementy do wykonania, Dodaj następujące metody do `TodoController` klasy:</span><span class="sxs-lookup"><span data-stu-id="5b19c-526">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="5b19c-527">Te metody implementują dwa punkty końcowe GET:</span><span class="sxs-lookup"><span data-stu-id="5b19c-527">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="5b19c-528">Zatrzymaj aplikację, jeśli jest nadal uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="5b19c-528">Stop the app if it's still running.</span></span> <span data-ttu-id="5b19c-529">Następnie uruchom ją ponownie, aby uwzględnić najnowsze zmiany.</span><span class="sxs-lookup"><span data-stu-id="5b19c-529">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="5b19c-530">Przetestuj aplikację, wywołując dwa punkty końcowe z przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="5b19c-530">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="5b19c-531">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="5b19c-531">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="5b19c-532">Następująca odpowiedź HTTP jest generowana przez wywołanie `GetTodoItems` :</span><span class="sxs-lookup"><span data-stu-id="5b19c-532">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="5b19c-533">Ścieżki routingu i adresów URL</span><span class="sxs-lookup"><span data-stu-id="5b19c-533">Routing and URL paths</span></span>

<span data-ttu-id="5b19c-534">Ten [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) atrybut oznacza metodę, która reaguje na żądanie HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="5b19c-534">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="5b19c-535">Ścieżka adresu URL dla każdej metody jest zbudowana w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="5b19c-535">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="5b19c-536">Zacznij od ciągu szablonu w `Route` atrybucie kontrolera:</span><span class="sxs-lookup"><span data-stu-id="5b19c-536">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="5b19c-537">Zastąp `[controller]` nazwą kontrolera, którą Konwencją jest nazwa klasy kontrolera minus sufiks "Controller".</span><span class="sxs-lookup"><span data-stu-id="5b19c-537">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="5b19c-538">W przypadku tego przykładu nazwa klasy kontrolera to kontroler do **zrobienia**, więc nazwa kontrolera to "do zrobienia".</span><span class="sxs-lookup"><span data-stu-id="5b19c-538">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="5b19c-539">W ASP.NET Core [routingu](xref:mvc/controllers/routing) jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="5b19c-539">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="5b19c-540">Jeśli `[HttpGet]` atrybut ma szablon trasy (na przykład `[HttpGet("products")]` ), Dodaj go do ścieżki.</span><span class="sxs-lookup"><span data-stu-id="5b19c-540">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="5b19c-541">Ten przykład nie używa szablonu.</span><span class="sxs-lookup"><span data-stu-id="5b19c-541">This sample doesn't use a template.</span></span> <span data-ttu-id="5b19c-542">Aby uzyskać więcej informacji, zobacz temat [Routing atrybutów z atrybutami http [Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="5b19c-542">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="5b19c-543">W poniższej `GetTodoItem` metodzie `"{id}"` jest zmienną zastępczą dla unikatowego identyfikatora elementu do wykonania.</span><span class="sxs-lookup"><span data-stu-id="5b19c-543">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="5b19c-544">Gdy `GetTodoItem` jest wywoływana, wartość `"{id}"` w adresie URL jest podawana do metody w jej `id` parametrze.</span><span class="sxs-lookup"><span data-stu-id="5b19c-544">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="5b19c-545">Zwracane wartości</span><span class="sxs-lookup"><span data-stu-id="5b19c-545">Return values</span></span>

<span data-ttu-id="5b19c-546">Zwracany typ `GetTodoItems` `GetTodoItem` metod i jest [ \<T> typem ActionResult](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="5b19c-546">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="5b19c-547">ASP.NET Core automatycznie serializować obiektu do [formatu JSON](https://www.json.org/) i zapisuje kod JSON w treści komunikatu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="5b19c-547">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="5b19c-548">Kod odpowiedzi dla tego typu zwracanego to 200, przy założeniu, że nie istnieją Nieobsłużone wyjątki.</span><span class="sxs-lookup"><span data-stu-id="5b19c-548">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="5b19c-549">Nieobsłużone wyjątki są tłumaczone na błędy 5xx.</span><span class="sxs-lookup"><span data-stu-id="5b19c-549">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="5b19c-550">`ActionResult`typy zwracane mogą reprezentować szeroką gamę kodów stanu HTTP.</span><span class="sxs-lookup"><span data-stu-id="5b19c-550">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="5b19c-551">Na przykład `GetTodoItem` może zwracać dwie różne wartości stanu:</span><span class="sxs-lookup"><span data-stu-id="5b19c-551">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="5b19c-552">Jeśli żaden element nie jest zgodny z żądanym IDENTYFIKATORem, metoda zwraca 404 kod błędu [NOTFOUND](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) .</span><span class="sxs-lookup"><span data-stu-id="5b19c-552">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="5b19c-553">W przeciwnym razie metoda zwraca 200 z treścią odpowiedzi JSON.</span><span class="sxs-lookup"><span data-stu-id="5b19c-553">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="5b19c-554">Zwracanie `item` wyników w odpowiedzi HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="5b19c-554">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="5b19c-555">Testowanie metody GetTodoItems</span><span class="sxs-lookup"><span data-stu-id="5b19c-555">Test the GetTodoItems method</span></span>

<span data-ttu-id="5b19c-556">Ten samouczek używa programu do testowania interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="5b19c-556">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="5b19c-557">Zainstaluj program [Poster](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="5b19c-557">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="5b19c-558">Uruchom aplikację internetową.</span><span class="sxs-lookup"><span data-stu-id="5b19c-558">Start the web app.</span></span>
* <span data-ttu-id="5b19c-559">Uruchom wpis.</span><span class="sxs-lookup"><span data-stu-id="5b19c-559">Start Postman.</span></span>
* <span data-ttu-id="5b19c-560">Wyłącz **weryfikację certyfikatu SSL**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-560">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5b19c-561">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5b19c-561">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5b19c-562">W **File** obszarze > **Ustawienia** pliku (karta**Ogólne** ) Wyłącz **weryfikację certyfikatu SSL**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-562">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="5b19c-563">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="5b19c-563">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="5b19c-564">Z poziomu preferencji programu **Poster**  >  **Preferences** (karta**Ogólne** ) Wyłącz **weryfikację certyfikatu SSL**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-564">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="5b19c-565">Alternatywnie wybierz klucz i wybierz pozycję **Ustawienia**, a następnie wyłącz weryfikację certyfikatu SSL.</span><span class="sxs-lookup"><span data-stu-id="5b19c-565">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="5b19c-566">Po przetestowaniu kontrolera ponownie Włącz weryfikację certyfikatu SSL.</span><span class="sxs-lookup"><span data-stu-id="5b19c-566">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="5b19c-567">Utwórz nowe żądanie.</span><span class="sxs-lookup"><span data-stu-id="5b19c-567">Create a new request.</span></span>
  * <span data-ttu-id="5b19c-568">Ustaw metodę HTTP, aby **uzyskać**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-568">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="5b19c-569">Ustaw adres URL żądania `https://localhost:<port>/api/todo` .</span><span class="sxs-lookup"><span data-stu-id="5b19c-569">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="5b19c-570">Na przykład `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="5b19c-570">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="5b19c-571">Ustaw **dwa widoki okienka** w programie Poster.</span><span class="sxs-lookup"><span data-stu-id="5b19c-571">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="5b19c-572">Wybierz pozycję **Send** (Wyślij).</span><span class="sxs-lookup"><span data-stu-id="5b19c-572">Select **Send**.</span></span>

![Ogłoś przy użyciu żądania GET](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="5b19c-574">Dodawanie metody Create</span><span class="sxs-lookup"><span data-stu-id="5b19c-574">Add a Create method</span></span>

<span data-ttu-id="5b19c-575">Dodaj następującą `PostTodoItem` metodę w obszarze *controllers/TodoController. cs*:</span><span class="sxs-lookup"><span data-stu-id="5b19c-575">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="5b19c-576">Poprzedni kod jest metodą POST protokołu HTTP, jak wskazano w [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) atrybucie.</span><span class="sxs-lookup"><span data-stu-id="5b19c-576">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="5b19c-577">Metoda pobiera wartość elementu do wykonania z treści żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="5b19c-577">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="5b19c-578">`CreatedAtAction`Metoda:</span><span class="sxs-lookup"><span data-stu-id="5b19c-578">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="5b19c-579">Zwraca kod stanu HTTP 201, jeśli powodzenie.</span><span class="sxs-lookup"><span data-stu-id="5b19c-579">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="5b19c-580">HTTP 201 to standardowa odpowiedź dla metody POST protokołu HTTP, która tworzy nowy zasób na serwerze.</span><span class="sxs-lookup"><span data-stu-id="5b19c-580">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="5b19c-581">Dodaje `Location` nagłówek do odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="5b19c-581">Adds a `Location` header to the response.</span></span> <span data-ttu-id="5b19c-582">`Location`Nagłówek określa identyfikator URI nowo utworzonego elementu do wykonania.</span><span class="sxs-lookup"><span data-stu-id="5b19c-582">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="5b19c-583">Aby uzyskać więcej informacji, zobacz [10.2.2 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="5b19c-583">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="5b19c-584">Odwołuje się do `GetTodoItem` akcji tworzenia `Location` identyfikatora URI nagłówka.</span><span class="sxs-lookup"><span data-stu-id="5b19c-584">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="5b19c-585">`nameof`Słowo kluczowe języka C# służy do zapobiegania twardemu kodowaniu nazwy akcji w `CreatedAtAction` wywołaniu.</span><span class="sxs-lookup"><span data-stu-id="5b19c-585">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="5b19c-586">Testowanie metody PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="5b19c-586">Test the PostTodoItem method</span></span>

* <span data-ttu-id="5b19c-587">Skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="5b19c-587">Build the project.</span></span>
* <span data-ttu-id="5b19c-588">W programie Poster ustaw metodę HTTP na `POST` .</span><span class="sxs-lookup"><span data-stu-id="5b19c-588">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="5b19c-589">Wybierz kartę **Treść**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-589">Select the **Body** tab.</span></span>
* <span data-ttu-id="5b19c-590">Wybierz przycisk radiowy **RAW** .</span><span class="sxs-lookup"><span data-stu-id="5b19c-590">Select the **raw** radio button.</span></span>
* <span data-ttu-id="5b19c-591">Ustaw typ na **JSON (Application/JSON)**.</span><span class="sxs-lookup"><span data-stu-id="5b19c-591">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="5b19c-592">W treści żądania wprowadź kod JSON dla elementu do wykonania:</span><span class="sxs-lookup"><span data-stu-id="5b19c-592">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="5b19c-593">Wybierz pozycję **Send** (Wyślij).</span><span class="sxs-lookup"><span data-stu-id="5b19c-593">Select **Send**.</span></span>

  ![Ogłoś przy użyciu żądania Create](first-web-api/_static/create.png)

  <span data-ttu-id="5b19c-595">Jeśli wystąpi błąd 405 metody niedozwolonej, jest to prawdopodobnie wynik niekompilowania projektu po dodaniu `PostTodoItem` metody.</span><span class="sxs-lookup"><span data-stu-id="5b19c-595">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="5b19c-596">Testowanie identyfikatora URI nagłówka lokalizacji</span><span class="sxs-lookup"><span data-stu-id="5b19c-596">Test the location header URI</span></span>

* <span data-ttu-id="5b19c-597">Wybierz kartę **nagłówki** w okienku **odpowiedź** .</span><span class="sxs-lookup"><span data-stu-id="5b19c-597">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="5b19c-598">Skopiuj wartość nagłówka **lokalizacji** :</span><span class="sxs-lookup"><span data-stu-id="5b19c-598">Copy the **Location** header value:</span></span>

  ![Karta nagłówki w konsoli programu Poster](first-web-api/_static/pmc2.png)

* <span data-ttu-id="5b19c-600">Ustaw metodę, aby uzyskać.</span><span class="sxs-lookup"><span data-stu-id="5b19c-600">Set the method to GET.</span></span>
* <span data-ttu-id="5b19c-601">Wklej URI (na przykład `https://localhost:5001/api/Todo/2` ).</span><span class="sxs-lookup"><span data-stu-id="5b19c-601">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="5b19c-602">Wybierz pozycję **Send** (Wyślij).</span><span class="sxs-lookup"><span data-stu-id="5b19c-602">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="5b19c-603">Dodawanie metody PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="5b19c-603">Add a PutTodoItem method</span></span>

<span data-ttu-id="5b19c-604">Dodaj następującą `PutTodoItem` metodę:</span><span class="sxs-lookup"><span data-stu-id="5b19c-604">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="5b19c-605">`PutTodoItem`jest podobny do `PostTodoItem` , z tą różnicą, że używa protokołu HTTP Put.</span><span class="sxs-lookup"><span data-stu-id="5b19c-605">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="5b19c-606">Odpowiedź to [204 (brak zawartości)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="5b19c-606">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="5b19c-607">Zgodnie ze specyfikacją protokołu HTTP żądanie PUT wymaga, aby klient wysłał całą zaktualizowaną jednostkę, a nie tylko te zmiany.</span><span class="sxs-lookup"><span data-stu-id="5b19c-607">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="5b19c-608">Aby zapewnić obsługę częściowych aktualizacji, użyj [poprawki http](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="5b19c-608">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="5b19c-609">Jeśli wystąpi błąd podczas wywoływania `PutTodoItem` , wywołaj, `GET` Aby upewnić się, że w bazie danych znajduje się element.</span><span class="sxs-lookup"><span data-stu-id="5b19c-609">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="5b19c-610">Testowanie metody PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="5b19c-610">Test the PutTodoItem method</span></span>

<span data-ttu-id="5b19c-611">Ten przykład korzysta z bazy danych w pamięci, która musi zostać zainicjowana za każdym razem, gdy aplikacja zostanie uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="5b19c-611">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="5b19c-612">Przed wykonaniem wywołania PUT musi istnieć element w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="5b19c-612">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="5b19c-613">Wywołaj polecenie GET, aby upewnić się, że w bazie danych znajduje się element, przed wykonaniem wywołania PUT.</span><span class="sxs-lookup"><span data-stu-id="5b19c-613">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="5b19c-614">Zaktualizuj element do wykonania o identyfikatorze 1 i ustaw jego nazwę na "Źródło danych":</span><span class="sxs-lookup"><span data-stu-id="5b19c-614">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="5b19c-615">Na poniższej ilustracji przedstawiono aktualizację programu Poster:</span><span class="sxs-lookup"><span data-stu-id="5b19c-615">The following image shows the Postman update:</span></span>

![Konsola programu Poster pokazująca odpowiedź 204 (brak zawartości)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="5b19c-617">Dodawanie metody DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="5b19c-617">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="5b19c-618">Dodaj następującą `DeleteTodoItem` metodę:</span><span class="sxs-lookup"><span data-stu-id="5b19c-618">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="5b19c-619">`DeleteTodoItem`Odpowiedź to [204 (brak zawartości)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="5b19c-619">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="5b19c-620">Testowanie metody DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="5b19c-620">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="5b19c-621">Użyj programu Poster, aby usunąć element do wykonania:</span><span class="sxs-lookup"><span data-stu-id="5b19c-621">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="5b19c-622">Ustaw metodę na `DELETE` .</span><span class="sxs-lookup"><span data-stu-id="5b19c-622">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="5b19c-623">Ustaw identyfikator URI obiektu do usunięcia (na przykład `https://localhost:5001/api/todo/1` ).</span><span class="sxs-lookup"><span data-stu-id="5b19c-623">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="5b19c-624">Wybierz pozycję **Send** (Wyślij).</span><span class="sxs-lookup"><span data-stu-id="5b19c-624">Select **Send**.</span></span>

<span data-ttu-id="5b19c-625">Przykładowa aplikacja umożliwia usunięcie wszystkich elementów.</span><span class="sxs-lookup"><span data-stu-id="5b19c-625">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="5b19c-626">Jednak po usunięciu ostatniego elementu jest on tworzony przez konstruktora klasy modelu przy następnym wywołaniu interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="5b19c-626">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="5b19c-627">Wywoływanie interfejsu API sieci Web przy użyciu języka JavaScript</span><span class="sxs-lookup"><span data-stu-id="5b19c-627">Call the web API with JavaScript</span></span>

<span data-ttu-id="5b19c-628">W tej sekcji zostanie dodana strona HTML, która używa języka JavaScript do wywoływania internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="5b19c-628">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="5b19c-629">jQuery inicjuje żądanie.</span><span class="sxs-lookup"><span data-stu-id="5b19c-629">jQuery initiates the request.</span></span> <span data-ttu-id="5b19c-630">Język JavaScript aktualizuje stronę ze szczegółowymi informacjami z odpowiedzi internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="5b19c-630">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="5b19c-631">Skonfiguruj aplikację do [obsługi plików statycznych](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) i [Włącz domyślne mapowanie plików](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) , aktualizując *Startup.cs* z następującym wyróżnionym kodem:</span><span class="sxs-lookup"><span data-stu-id="5b19c-631">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="5b19c-632">Utwórz folder *wwwroot* w katalogu projektu.</span><span class="sxs-lookup"><span data-stu-id="5b19c-632">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="5b19c-633">Dodaj plik HTML o nazwie *index.html* do katalogu *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="5b19c-633">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="5b19c-634">Zastąp jego zawartość następującym znacznikiem:</span><span class="sxs-lookup"><span data-stu-id="5b19c-634">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="5b19c-635">Dodaj plik języka JavaScript o nazwie *site.js* do katalogu *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="5b19c-635">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="5b19c-636">Zastąp jego zawartość następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="5b19c-636">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="5b19c-637">Zmiana ustawień uruchamiania projektu ASP.NET Core może być wymagana do lokalnego przetestowania strony HTML:</span><span class="sxs-lookup"><span data-stu-id="5b19c-637">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="5b19c-638">Otwórz *Properties\launchSettings.jsna*.</span><span class="sxs-lookup"><span data-stu-id="5b19c-638">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="5b19c-639">Usuń `launchUrl` Właściwość, aby wymusić otwieranie przez aplikację w *index.html* &mdash; domyślnego pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="5b19c-639">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="5b19c-640">Ten przykład wywołuje wszystkie metody CRUD internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="5b19c-640">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="5b19c-641">Poniżej znajdują się wyjaśnienia wywołań interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="5b19c-641">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="5b19c-642">Pobierz listę elementów do wykonania</span><span class="sxs-lookup"><span data-stu-id="5b19c-642">Get a list of to-do items</span></span>

<span data-ttu-id="5b19c-643">jQuery wysyła żądanie HTTP GET do internetowego interfejsu API, który zwraca kod JSON reprezentujący tablicę elementów do wykonania.</span><span class="sxs-lookup"><span data-stu-id="5b19c-643">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="5b19c-644">`success`Funkcja wywołania zwrotnego jest wywoływana, jeśli żądanie zakończy się pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="5b19c-644">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="5b19c-645">W wywołaniu zwrotnym model DOM jest aktualizowany przy użyciu informacji o tym do wykonania.</span><span class="sxs-lookup"><span data-stu-id="5b19c-645">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="5b19c-646">Dodaj element do wykonania</span><span class="sxs-lookup"><span data-stu-id="5b19c-646">Add a to-do item</span></span>

<span data-ttu-id="5b19c-647">jQuery wysyła żądanie HTTP POST z elementem do wykonania w treści żądania.</span><span class="sxs-lookup"><span data-stu-id="5b19c-647">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="5b19c-648">`accepts`Opcje i `contentType` są ustawione na, aby `application/json` określić typ nośnika odbierany i wysyłany.</span><span class="sxs-lookup"><span data-stu-id="5b19c-648">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="5b19c-649">Element do wykonania jest konwertowany na format JSON przy użyciu [formatu JSON. stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="5b19c-649">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="5b19c-650">Gdy interfejs API zwraca kod stanu pomyślnego, `getData` Funkcja jest wywoływana w celu zaktualizowania tabeli HTML.</span><span class="sxs-lookup"><span data-stu-id="5b19c-650">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="5b19c-651">Aktualizowanie elementu do wykonania</span><span class="sxs-lookup"><span data-stu-id="5b19c-651">Update a to-do item</span></span>

<span data-ttu-id="5b19c-652">Aktualizowanie elementu do wykonania jest podobne do dodawania jednego.</span><span class="sxs-lookup"><span data-stu-id="5b19c-652">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="5b19c-653">`url`Zmiany mające na celu dodanie unikatowego identyfikatora elementu i `type` is `PUT` .</span><span class="sxs-lookup"><span data-stu-id="5b19c-653">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="5b19c-654">Usuń element do wykonania</span><span class="sxs-lookup"><span data-stu-id="5b19c-654">Delete a to-do item</span></span>

<span data-ttu-id="5b19c-655">Usuwanie elementu do wykonania jest realizowane przez ustawienie `type` w WYWOŁANIU AJAX `DELETE` i określenie unikatowego identyfikatora elementu w adresie URL.</span><span class="sxs-lookup"><span data-stu-id="5b19c-655">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="5b19c-656">Dodawanie obsługi uwierzytelniania do internetowego interfejsu API</span><span class="sxs-lookup"><span data-stu-id="5b19c-656">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a><span data-ttu-id="5b19c-657">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="5b19c-657">Additional resources</span></span>

<span data-ttu-id="5b19c-658">[Wyświetl lub Pobierz przykładowy kod dla tego samouczka](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="5b19c-658">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="5b19c-659">Zobacz artykuł [jak pobrać](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="5b19c-659">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="5b19c-660">Więcej informacji zawierają następujące zasoby:</span><span class="sxs-lookup"><span data-stu-id="5b19c-660">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="5b19c-661">Wersja tego samouczka usługi YouTube</span><span class="sxs-lookup"><span data-stu-id="5b19c-661">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
