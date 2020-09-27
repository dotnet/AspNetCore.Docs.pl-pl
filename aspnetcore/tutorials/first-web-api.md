---
title: 'Samouczek: Tworzenie internetowego interfejsu API za pomocą ASP.NET Core'
author: rick-anderson
description: Dowiedz się, jak utworzyć internetowy interfejs API za pomocą ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2020
no-loc:
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
- Models
uid: tutorials/first-web-api
ms.openlocfilehash: 212d8a80bdc466479c34bc5fbd9c3261ca9d54c4
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393915"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="89fb3-103">Samouczek: Tworzenie internetowego interfejsu API za pomocą ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="89fb3-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="89fb3-104">Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirka Larkin](https://twitter.com/serpent5)i [Jan Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="89fb3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="89fb3-105">Ten samouczek uczy się podstaw tworzenia interfejsu API sieci Web za pomocą ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="89fb3-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="89fb3-106">Ten samouczek zawiera informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="89fb3-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="89fb3-107">Utwórz projekt interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="89fb3-107">Create a web API project.</span></span>
> * <span data-ttu-id="89fb3-108">Dodaj klasę modelu i kontekst bazy danych.</span><span class="sxs-lookup"><span data-stu-id="89fb3-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="89fb3-109">Tworzy szkielet kontrolera z metodami CRUD.</span><span class="sxs-lookup"><span data-stu-id="89fb3-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="89fb3-110">Skonfiguruj Routing, ścieżki URL i wartości zwracane.</span><span class="sxs-lookup"><span data-stu-id="89fb3-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="89fb3-111">Wywołaj interfejs API sieci Web za pomocą programu Poster.</span><span class="sxs-lookup"><span data-stu-id="89fb3-111">Call the web API with Postman.</span></span>

<span data-ttu-id="89fb3-112">Na końcu znajduje się internetowy interfejs API, który może zarządzać elementami do wykonania przechowywanymi w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="89fb3-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="89fb3-113">Omówienie</span><span class="sxs-lookup"><span data-stu-id="89fb3-113">Overview</span></span>

<span data-ttu-id="89fb3-114">Ten samouczek tworzy następujący interfejs API:</span><span class="sxs-lookup"><span data-stu-id="89fb3-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="89fb3-115">Interfejs API</span><span class="sxs-lookup"><span data-stu-id="89fb3-115">API</span></span> | <span data-ttu-id="89fb3-116">Opis</span><span class="sxs-lookup"><span data-stu-id="89fb3-116">Description</span></span> | <span data-ttu-id="89fb3-117">Treść żądania</span><span class="sxs-lookup"><span data-stu-id="89fb3-117">Request body</span></span> | <span data-ttu-id="89fb3-118">Treść odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="89fb3-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="89fb3-119">Pobierz wszystkie elementy do wykonania</span><span class="sxs-lookup"><span data-stu-id="89fb3-119">Get all to-do items</span></span> | <span data-ttu-id="89fb3-120">Brak</span><span class="sxs-lookup"><span data-stu-id="89fb3-120">None</span></span> | <span data-ttu-id="89fb3-121">Tablica elementów do wykonania</span><span class="sxs-lookup"><span data-stu-id="89fb3-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="89fb3-122">Pobieranie elementu według identyfikatora</span><span class="sxs-lookup"><span data-stu-id="89fb3-122">Get an item by ID</span></span> | <span data-ttu-id="89fb3-123">Brak</span><span class="sxs-lookup"><span data-stu-id="89fb3-123">None</span></span> | <span data-ttu-id="89fb3-124">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="89fb3-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="89fb3-125">Dodaj nowy element</span><span class="sxs-lookup"><span data-stu-id="89fb3-125">Add a new item</span></span> | <span data-ttu-id="89fb3-126">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="89fb3-126">To-do item</span></span> | <span data-ttu-id="89fb3-127">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="89fb3-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="89fb3-128">Aktualizowanie istniejącego elementu &nbsp;</span><span class="sxs-lookup"><span data-stu-id="89fb3-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="89fb3-129">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="89fb3-129">To-do item</span></span> | <span data-ttu-id="89fb3-130">Brak</span><span class="sxs-lookup"><span data-stu-id="89fb3-130">None</span></span> |
|<span data-ttu-id="89fb3-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="89fb3-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="89fb3-132">Usuń element &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="89fb3-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="89fb3-133">Brak</span><span class="sxs-lookup"><span data-stu-id="89fb3-133">None</span></span> | <span data-ttu-id="89fb3-134">Brak</span><span class="sxs-lookup"><span data-stu-id="89fb3-134">None</span></span>|

<span data-ttu-id="89fb3-135">Na poniższym diagramie przedstawiono projekt aplikacji.</span><span class="sxs-lookup"><span data-stu-id="89fb3-135">The following diagram shows the design of the app.</span></span>

![Klient jest reprezentowany przez pole po lewej stronie.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="89fb3-141">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="89fb3-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="89fb3-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="89fb3-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="89fb3-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="89fb3-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="89fb3-144">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="89fb3-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="89fb3-145">Tworzenie projektu sieci Web</span><span class="sxs-lookup"><span data-stu-id="89fb3-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="89fb3-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="89fb3-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="89fb3-147">Z menu **plik** wybierz pozycję **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="89fb3-148">Wybierz szablon **aplikacja sieci Web ASP.NET Core** a następnie kliknij przycisk **dalej**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="89fb3-149">Nazwij projekt *TodoApi* i kliknij pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="89fb3-150">W oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** upewnij się, że wybrano opcję **.net Core** i **ASP.NET Core 5,0** .</span><span class="sxs-lookup"><span data-stu-id="89fb3-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 5.0** are selected.</span></span> <span data-ttu-id="89fb3-151">Wybierz szablon **interfejsu API** i kliknij przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-151">Select the **API** template and click **Create**.</span></span>

![Okno dialogowe programu VS New Project](first-web-api/_static/5/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="89fb3-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="89fb3-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="89fb3-154">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="89fb3-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="89fb3-155">Zmień katalog ( `cd` ) do folderu, który będzie zawierać folder projektu.</span><span class="sxs-lookup"><span data-stu-id="89fb3-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="89fb3-156">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="89fb3-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="89fb3-157">Gdy zostanie wyświetlone okno dialogowe z pytaniem, czy chcesz dodać wymagane zasoby do projektu, wybierz opcję **tak**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="89fb3-158">Poprzednie polecenia:</span><span class="sxs-lookup"><span data-stu-id="89fb3-158">The preceding commands:</span></span>

  * <span data-ttu-id="89fb3-159">Tworzy nowy projekt internetowego interfejsu API i otwiera go w Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="89fb3-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="89fb3-160">Dodaje pakiety NuGet, które są wymagane w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="89fb3-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="89fb3-161">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="89fb3-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="89fb3-162">Wybierz pozycję **plik** > **nowe rozwiązanie**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-162">Select **File** > **New Solution**.</span></span>

  ![macOS nowe rozwiązanie](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="89fb3-164">W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz **.NET Core**pozycję  >  **App**  >  **interfejs API**aplikacji .NET Core  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="89fb3-165">W wersji 8,6 lub nowszej wybierz pozycję **Web and Console**  >  **App**  >  **interfejs API**  >  **Next**aplikacji sieci Web i konsoli.</span><span class="sxs-lookup"><span data-stu-id="89fb3-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![Wybór szablonu interfejsu API macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="89fb3-167">W oknie dialogowym **Konfigurowanie nowego interfejsu API sieci Web ASP.NET Core** wybierz najnowszą platformę **docelową**.NET Core 3. x.</span><span class="sxs-lookup"><span data-stu-id="89fb3-167">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="89fb3-168">Wybierz opcję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-168">Select **Next**.</span></span>

* <span data-ttu-id="89fb3-169">Wprowadź *TodoApi* jako **nazwę projektu** , a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![okno dialogowe konfiguracji](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="89fb3-171">Otwórz Terminal poleceń w folderze projektu i uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="89fb3-171">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-project"></a><span data-ttu-id="89fb3-172">Testowanie projektu</span><span class="sxs-lookup"><span data-stu-id="89fb3-172">Test the project</span></span>

<span data-ttu-id="89fb3-173">Szablon projektu tworzy `WeatherForecast` interfejs API z obsługą [struktury Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span><span class="sxs-lookup"><span data-stu-id="89fb3-173">The project template creates a `WeatherForecast` API with support for [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="89fb3-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="89fb3-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="89fb3-175">Naciśnij klawisze CTRL + F5, aby uruchomić bez debugera.</span><span class="sxs-lookup"><span data-stu-id="89fb3-175">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="89fb3-176">Zostanie uruchomiony program Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="89fb3-176">Visual Studio launches:</span></span>

* <span data-ttu-id="89fb3-177">Serwer sieci Web IIS Express.</span><span class="sxs-lookup"><span data-stu-id="89fb3-177">The IIS Express web server.</span></span>
* <span data-ttu-id="89fb3-178">Domyślna przeglądarka i nawiguje do `https://localhost:<port>/https://localhost:5001/swagger/index.html` , gdzie `<port>` jest losowo wybranym numerem portu.</span><span class="sxs-lookup"><span data-stu-id="89fb3-178">The default browser and navigates to `https://localhost:<port>/https://localhost:5001/swagger/index.html`, where `<port>` is a randomly chosen port number.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="89fb3-179">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="89fb3-179">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/trustCertVSC.md)]

<span data-ttu-id="89fb3-180">Naciśnij klawisze CTRL + F5, aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="89fb3-180">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="89fb3-181">W przeglądarce przejdź do następującego adresu URL: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span><span class="sxs-lookup"><span data-stu-id="89fb3-181">In a browser, go to following URL: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="89fb3-182">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="89fb3-182">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="89fb3-183">Wybierz pozycję **Uruchom**  >  **Rozpocznij debugowanie** , aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="89fb3-183">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="89fb3-184">Visual Studio dla komputerów Mac uruchamia przeglądarkę i nawiguje do `https://localhost:<port>` , gdzie `<port>` jest losowo wybranym numerem portu.</span><span class="sxs-lookup"><span data-stu-id="89fb3-184">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="89fb3-185">Zwracany jest błąd HTTP 404 (nie znaleziono).</span><span class="sxs-lookup"><span data-stu-id="89fb3-185">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="89fb3-186">Dołącz `/swagger` do adresu URL (Zmień adres URL na `https://localhost:<port>/swagger` ).</span><span class="sxs-lookup"><span data-stu-id="89fb3-186">Append `/swagger` to the URL (change the URL to `https://localhost:<port>/swagger`).</span></span>

---

<span data-ttu-id="89fb3-187">`/swagger/index.html`Zostanie wyświetlona strona Swagger.</span><span class="sxs-lookup"><span data-stu-id="89fb3-187">The Swagger page `/swagger/index.html` is displayed.</span></span> <span data-ttu-id="89fb3-188">Wybierz pozycję **Pobierz**  >  **spróbuj**  >  **wykonać**operację.</span><span class="sxs-lookup"><span data-stu-id="89fb3-188">Select **GET** > **Try it out** > **Execute**.</span></span> <span data-ttu-id="89fb3-189">Zostanie wyświetlona strona:</span><span class="sxs-lookup"><span data-stu-id="89fb3-189">The page displays:</span></span>

* <span data-ttu-id="89fb3-190">[Zwinięcie](https://curl.haxx.se/) polecenie do testowania interfejsu API WeatherForecast.</span><span class="sxs-lookup"><span data-stu-id="89fb3-190">The [Curl](https://curl.haxx.se/) command to test the WeatherForecast API.</span></span>
* <span data-ttu-id="89fb3-191">Adres URL służący do testowania interfejsu API WeatherForecast.</span><span class="sxs-lookup"><span data-stu-id="89fb3-191">The URL to test the WeatherForecast API.</span></span>
* <span data-ttu-id="89fb3-192">Kod odpowiedzi, treść i nagłówki.</span><span class="sxs-lookup"><span data-stu-id="89fb3-192">The response code, body, and headers.</span></span>
* <span data-ttu-id="89fb3-193">Pole listy rozwijanej z typami nośników oraz przykładową wartością i schematem.</span><span class="sxs-lookup"><span data-stu-id="89fb3-193">A drop down list box with media types and the example value and schema.</span></span>

<!-- Review: Do we care the IE generates several errors. It shows the data, but with  Unrecognized response type; displaying content as text.
-->
<span data-ttu-id="89fb3-194">Struktura Swagger służy do generowania przydatnej dokumentacji i stron pomocy dla interfejsów API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="89fb3-194">Swagger is used to generate useful documentation and help pages for web APIs.</span></span> <span data-ttu-id="89fb3-195">Ten samouczek koncentruje się na tworzeniu interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="89fb3-195">This tutorial focuses on creating a web API.</span></span> <span data-ttu-id="89fb3-196">Aby uzyskać więcej informacji na temat struktury Swagger, zobacz <xref:tutorials/web-api-help-pages-using-swagger> .</span><span class="sxs-lookup"><span data-stu-id="89fb3-196">For more information on Swagger, see <xref:tutorials/web-api-help-pages-using-swagger>.</span></span>

<span data-ttu-id="89fb3-197">Skopiuj i wklej **adres URL żądania** w przeglądarce:  `https://localhost:<port>/WeatherForecast`</span><span class="sxs-lookup"><span data-stu-id="89fb3-197">Copy and past the **Request URL** in the browser:  `https://localhost:<port>/WeatherForecast`</span></span>

<span data-ttu-id="89fb3-198">Zwracany jest kod JSON podobny do następującego:</span><span class="sxs-lookup"><span data-stu-id="89fb3-198">JSON similar to the following is returned:</span></span>

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

### <a name="update-the-launchurl"></a><span data-ttu-id="89fb3-199">Aktualizowanie launchUrl</span><span class="sxs-lookup"><span data-stu-id="89fb3-199">Update the launchUrl</span></span>

<span data-ttu-id="89fb3-200">W *Properties\launchSettings.json*Update `launchUrl` od `"swagger"` do `"api/TodoItems"` :</span><span class="sxs-lookup"><span data-stu-id="89fb3-200">In *Properties\launchSettings.json*, update `launchUrl` from `"swagger"` to `"api/TodoItems"`:</span></span>

```json
"launchUrl": "api/TodoItems",
```

<span data-ttu-id="89fb3-201">Ze względu na to, że program Swagger został usunięty, poprzedzający znacznik zmienia adres URL, który jest uruchamiany w metodzie GET kontrolera dodanej w poniższych sekcjach.</span><span class="sxs-lookup"><span data-stu-id="89fb3-201">Because Swagger has been removed, the preceding markup changes the URL that is launched to the GET method of the controller added in the following sections.</span></span>

## <a name="add-a-model-class"></a><span data-ttu-id="89fb3-202">Dodaj klasę modelu</span><span class="sxs-lookup"><span data-stu-id="89fb3-202">Add a model class</span></span>

<span data-ttu-id="89fb3-203">*Model* to zestaw klas, które reprezentują dane zarządzane przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="89fb3-203">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="89fb3-204">Model tej aplikacji jest pojedynczą `TodoItem` klasą.</span><span class="sxs-lookup"><span data-stu-id="89fb3-204">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="89fb3-205">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="89fb3-205">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="89fb3-206">W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt.</span><span class="sxs-lookup"><span data-stu-id="89fb3-206">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="89fb3-207">Wybierz pozycję **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-207">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="89fb3-208">Nazwij folder *Models* .</span><span class="sxs-lookup"><span data-stu-id="89fb3-208">Name the folder *Models*.</span></span>

* <span data-ttu-id="89fb3-209">Kliknij prawym przyciskiem myszy *Models* folder i wybierz polecenie **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-209">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="89fb3-210">Nadaj klasie nazwę *TodoItem* i wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-210">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="89fb3-211">Zastąp kod szablonu następującym:</span><span class="sxs-lookup"><span data-stu-id="89fb3-211">Replace the template code with the following:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="89fb3-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="89fb3-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="89fb3-213">Dodaj folder o nazwie *Models* .</span><span class="sxs-lookup"><span data-stu-id="89fb3-213">Add a folder named *Models*.</span></span>

* <span data-ttu-id="89fb3-214">Dodaj `TodoItem` do *Models* folderu klasę o następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="89fb3-214">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="89fb3-215">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="89fb3-215">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="89fb3-216">Kliknij prawym przyciskiem myszy projekt.</span><span class="sxs-lookup"><span data-stu-id="89fb3-216">Right-click the project.</span></span> <span data-ttu-id="89fb3-217">Wybierz pozycję **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-217">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="89fb3-218">Nazwij folder *Models* .</span><span class="sxs-lookup"><span data-stu-id="89fb3-218">Name the folder *Models*.</span></span>

  ![Nowy folder](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="89fb3-220">Kliknij prawym przyciskiem myszy *Models* folder, a następnie wybierz pozycję **Dodaj** > **nowy plik** > **ogólna** > **pusta Klasa**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-220">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="89fb3-221">Nazwij klasę *TodoItem*, a następnie kliknij pozycję **New (nowy**).</span><span class="sxs-lookup"><span data-stu-id="89fb3-221">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="89fb3-222">Zastąp kod szablonu następującym:</span><span class="sxs-lookup"><span data-stu-id="89fb3-222">Replace the template code with the following:</span></span>

---

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="89fb3-223">`Id`Właściwość działa jako unikatowy klucz w relacyjnej bazie danych.</span><span class="sxs-lookup"><span data-stu-id="89fb3-223">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="89fb3-224">Klasy modelu mogą przejść do dowolnego miejsca w projekcie, ale *Models* folder jest używany przez Konwencję.</span><span class="sxs-lookup"><span data-stu-id="89fb3-224">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="89fb3-225">Dodawanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="89fb3-225">Add a database context</span></span>

<span data-ttu-id="89fb3-226">*Kontekst bazy danych* jest główną klasą, która koordynuje Entity Framework funkcji dla modelu danych.</span><span class="sxs-lookup"><span data-stu-id="89fb3-226">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="89fb3-227">Ta klasa jest tworzona przez wyprowadzanie z <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> klasy.</span><span class="sxs-lookup"><span data-stu-id="89fb3-227">This class is created by deriving from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="89fb3-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="89fb3-228">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="89fb3-229">Dodawanie pakietów NuGet</span><span class="sxs-lookup"><span data-stu-id="89fb3-229">Add NuGet packages</span></span>

* <span data-ttu-id="89fb3-230">W menu **Narzędzia** wybierz pozycję **menedżer pakietów NuGet > zarządzanie pakietami NuGet dla rozwiązania**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-230">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="89fb3-231">Wybierz kartę **Przeglądaj** , a następnie wpisz \* \* Microsoft.</span><span class="sxs-lookup"><span data-stu-id="89fb3-231">Select the **Browse** tab, and then enter \*\*Microsoft.</span></span>
<span data-ttu-id="89fb3-232">**EntityFrameworkCore. SqlServer** w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="89fb3-232">**EntityFrameworkCore.SqlServer** in the search box.</span></span>
<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Delete this line at RTM -->
* <span data-ttu-id="89fb3-233">Zaznacz pole wyboru **Uwzględnij wersję wstępną** , aby uzyskać dostęp do wersji 5,0 RC.</span><span class="sxs-lookup"><span data-stu-id="89fb3-233">Select the **Include prerelease** checkbox so the 5.0 RC version is available.</span></span> 
* <span data-ttu-id="89fb3-234">W lewym okienku wybierz pozycję **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="89fb3-234">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="89fb3-235">Zaznacz pole wyboru **projekt** w prawym okienku, a następnie wybierz pozycję **Zainstaluj**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-235">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="89fb3-236">Użyj powyższych instrukcji, aby dodać pakiet NuGet **Microsoft. EntityFrameworkCore. inMemory** .</span><span class="sxs-lookup"><span data-stu-id="89fb3-236">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Update this image at RTM -->
<span data-ttu-id="89fb3-237">![Menedżer pakietów NuGet](first-web-api/_static/5/vsNuGet.png)</span><span class="sxs-lookup"><span data-stu-id="89fb3-237">![NuGet Package Manager](first-web-api/_static/5/vsNuGet.png)</span></span>

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="89fb3-238">Dodawanie kontekstu bazy danych TodoContext</span><span class="sxs-lookup"><span data-stu-id="89fb3-238">Add the TodoContext database context</span></span>

* <span data-ttu-id="89fb3-239">Kliknij prawym przyciskiem myszy *Models* folder i wybierz polecenie **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-239">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="89fb3-240">Nadaj klasie nazwę *TodoContext* i kliknij przycisk **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-240">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="89fb3-241">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="89fb3-241">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="89fb3-242">Dodaj `TodoContext` klasę do *Models* folderu.</span><span class="sxs-lookup"><span data-stu-id="89fb3-242">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="89fb3-243">Wprowadź następujący kod:</span><span class="sxs-lookup"><span data-stu-id="89fb3-243">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="89fb3-244">Rejestrowanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="89fb3-244">Register the database context</span></span>

<span data-ttu-id="89fb3-245">W ASP.NET Core usługi, takie jak kontekst bazy danych, muszą być zarejestrowane z kontenerem [iniekcji zależności (di)](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="89fb3-245">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="89fb3-246">Kontener udostępnia usługę kontrolerom.</span><span class="sxs-lookup"><span data-stu-id="89fb3-246">The container provides the service to controllers.</span></span>

<span data-ttu-id="89fb3-247">Zaktualizuj *Startup.cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="89fb3-247">Update *Startup.cs* with the following code:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="89fb3-248">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="89fb3-248">The preceding code:</span></span>

* <span data-ttu-id="89fb3-249">Usuwa wywołania struktury Swagger.</span><span class="sxs-lookup"><span data-stu-id="89fb3-249">Removes the Swagger calls.</span></span>
* <span data-ttu-id="89fb3-250">Usuwa nieużywane `using` deklaracje.</span><span class="sxs-lookup"><span data-stu-id="89fb3-250">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="89fb3-251">Dodaje kontekst bazy danych do kontenera DI.</span><span class="sxs-lookup"><span data-stu-id="89fb3-251">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="89fb3-252">Określa, że kontekst bazy danych będzie używać bazy danych w pamięci.</span><span class="sxs-lookup"><span data-stu-id="89fb3-252">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="89fb3-253">Tworzenie szkieletu kontrolera</span><span class="sxs-lookup"><span data-stu-id="89fb3-253">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="89fb3-254">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="89fb3-254">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="89fb3-255">Kliknij prawym przyciskiem myszy folder *controllers* .</span><span class="sxs-lookup"><span data-stu-id="89fb3-255">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="89fb3-256">Wybierz pozycję **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-256">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="89fb3-257">Wybierz pozycję **kontroler interfejsu API z akcjami, używając Entity Framework**, a następnie wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-257">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="89fb3-258">Na stronie **Dodawanie kontrolera interfejsu API z akcjami przy użyciu Entity Framework** dialogowego:</span><span class="sxs-lookup"><span data-stu-id="89fb3-258">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="89fb3-259">Wybierz pozycję **TodoItem (TodoApi. Models )** w **klasie model**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-259">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="89fb3-260">Wybierz pozycję **TodoContext (TodoApi. Models )** w **klasie kontekstu danych**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-260">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="89fb3-261">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-261">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="89fb3-262">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="89fb3-262">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="89fb3-263">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="89fb3-263">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="89fb3-264">Poprzednie polecenia:</span><span class="sxs-lookup"><span data-stu-id="89fb3-264">The preceding commands:</span></span>

* <span data-ttu-id="89fb3-265">Dodaj pakiety NuGet wymagane do tworzenia szkieletów.</span><span class="sxs-lookup"><span data-stu-id="89fb3-265">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="89fb3-266">Instaluje aparat tworzenia szkieletu ( `dotnet-aspnet-codegenerator` ).</span><span class="sxs-lookup"><span data-stu-id="89fb3-266">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="89fb3-267">Szkielety `TodoItemsController` .</span><span class="sxs-lookup"><span data-stu-id="89fb3-267">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="89fb3-268">Wygenerowany kod:</span><span class="sxs-lookup"><span data-stu-id="89fb3-268">The generated code:</span></span>

* <span data-ttu-id="89fb3-269">Oznacza klasę [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atrybutem.</span><span class="sxs-lookup"><span data-stu-id="89fb3-269">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="89fb3-270">Ten atrybut wskazuje, że kontroler odpowiada na żądania interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="89fb3-270">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="89fb3-271">Aby uzyskać informacje o określonych zachowaniach, które włącza atrybut, zobacz <xref:web-api/index> .</span><span class="sxs-lookup"><span data-stu-id="89fb3-271">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="89fb3-272">Używa funkcji DI do iniekcji kontekstu bazy danych ( `TodoContext` ) do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="89fb3-272">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="89fb3-273">Kontekst bazy danych jest używany w każdej z metod [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) w kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="89fb3-273">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="89fb3-274">Szablony ASP.NET Core dla:</span><span class="sxs-lookup"><span data-stu-id="89fb3-274">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="89fb3-275">Kontrolery z widokami obejmują `[action]` szablon trasy.</span><span class="sxs-lookup"><span data-stu-id="89fb3-275">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="89fb3-276">Kontrolery interfejsu API nie należą `[action]` do szablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="89fb3-276">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="89fb3-277">Gdy `[action]` token nie znajduje się w szablonie trasy, nazwa [akcji](xref:mvc/controllers/routing#action) jest wykluczona z trasy.</span><span class="sxs-lookup"><span data-stu-id="89fb3-277">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="89fb3-278">Oznacza to, że nazwa metody skojarzonej z akcją nie jest używana w zgodnej trasie.</span><span class="sxs-lookup"><span data-stu-id="89fb3-278">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="update-the-posttodoitem-create-method"></a><span data-ttu-id="89fb3-279">Aktualizowanie metody PostTodoItem Create</span><span class="sxs-lookup"><span data-stu-id="89fb3-279">Update the PostTodoItem create method</span></span>

<span data-ttu-id="89fb3-280">Zastąp instrukcję return w, `PostTodoItem` Aby użyć operatora [nameof](/dotnet/csharp/language-reference/operators/nameof) :</span><span class="sxs-lookup"><span data-stu-id="89fb3-280">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="89fb3-281">Poprzedni kod jest metodą POST protokołu HTTP, jak wskazano w [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) atrybucie.</span><span class="sxs-lookup"><span data-stu-id="89fb3-281">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="89fb3-282">Metoda pobiera wartość elementu do wykonania z treści żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="89fb3-282">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="89fb3-283">Aby uzyskać więcej informacji, zobacz temat [Routing atrybutów z atrybutami http [Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="89fb3-283">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="89fb3-284"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>Metoda:</span><span class="sxs-lookup"><span data-stu-id="89fb3-284">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="89fb3-285">W razie powodzenia zwraca [kod stanu HTTP 201](https://developer.mozilla.org/docs/Web/HTTP/Status/201) .</span><span class="sxs-lookup"><span data-stu-id="89fb3-285">Returns an [HTTP 201 status code](https://developer.mozilla.org/docs/Web/HTTP/Status/201) if successful.</span></span> <span data-ttu-id="89fb3-286">HTTP 201 to standardowa odpowiedź dla metody POST protokołu HTTP, która tworzy nowy zasób na serwerze.</span><span class="sxs-lookup"><span data-stu-id="89fb3-286">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="89fb3-287">Dodaje nagłówek [lokalizacji](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) do odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="89fb3-287">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="89fb3-288">`Location`Nagłówek określa [Identyfikator URI](https://developer.mozilla.org/docs/Glossary/URI) nowo utworzonego elementu do wykonania.</span><span class="sxs-lookup"><span data-stu-id="89fb3-288">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="89fb3-289">Aby uzyskać więcej informacji, zobacz [10.2.2 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="89fb3-289">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="89fb3-290">Odwołuje się do `GetTodoItem` akcji tworzenia `Location` identyfikatora URI nagłówka.</span><span class="sxs-lookup"><span data-stu-id="89fb3-290">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="89fb3-291">`nameof`Słowo kluczowe języka C# służy do zapobiegania twardemu kodowaniu nazwy akcji w `CreatedAtAction` wywołaniu.</span><span class="sxs-lookup"><span data-stu-id="89fb3-291">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="89fb3-292">Zainstaluj program Poster</span><span class="sxs-lookup"><span data-stu-id="89fb3-292">Install Postman</span></span>

<span data-ttu-id="89fb3-293">Ten samouczek używa programu do testowania interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="89fb3-293">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="89fb3-294">Zainstaluj program [Poster](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="89fb3-294">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="89fb3-295">Uruchom aplikację internetową.</span><span class="sxs-lookup"><span data-stu-id="89fb3-295">Start the web app.</span></span>
* <span data-ttu-id="89fb3-296">Uruchom wpis.</span><span class="sxs-lookup"><span data-stu-id="89fb3-296">Start Postman.</span></span>
* <span data-ttu-id="89fb3-297">Wyłącz **weryfikację certyfikatu SSL**</span><span class="sxs-lookup"><span data-stu-id="89fb3-297">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="89fb3-298">W **File** obszarze > **Ustawienia** pliku (karta**Ogólne** ) Wyłącz **weryfikację certyfikatu SSL**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-298">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="89fb3-299">Po przetestowaniu kontrolera ponownie Włącz weryfikację certyfikatu SSL.</span><span class="sxs-lookup"><span data-stu-id="89fb3-299">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="89fb3-300">Test PostTodoItem za pomocą programu Poster</span><span class="sxs-lookup"><span data-stu-id="89fb3-300">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="89fb3-301">Utwórz nowe żądanie.</span><span class="sxs-lookup"><span data-stu-id="89fb3-301">Create a new request.</span></span>
* <span data-ttu-id="89fb3-302">Ustaw metodę HTTP na `POST` .</span><span class="sxs-lookup"><span data-stu-id="89fb3-302">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="89fb3-303">Ustaw identyfikator URI na `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="89fb3-303">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="89fb3-304">Na przykład `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="89fb3-304">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="89fb3-305">Wybierz kartę **Treść**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-305">Select the **Body** tab.</span></span>
* <span data-ttu-id="89fb3-306">Wybierz przycisk radiowy **RAW** .</span><span class="sxs-lookup"><span data-stu-id="89fb3-306">Select the **raw** radio button.</span></span>
* <span data-ttu-id="89fb3-307">Ustaw typ na **JSON (Application/JSON)**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-307">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="89fb3-308">W treści żądania wprowadź kod JSON dla elementu do wykonania:</span><span class="sxs-lookup"><span data-stu-id="89fb3-308">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="89fb3-309">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-309">Select **Send**.</span></span>

  ![Ogłoś przy użyciu żądania Create](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="89fb3-311">Testowanie identyfikatora URI nagłówka lokalizacji</span><span class="sxs-lookup"><span data-stu-id="89fb3-311">Test the location header URI</span></span>

<span data-ttu-id="89fb3-312">Identyfikator URI nagłówka lokalizacji może zostać przetestowany w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="89fb3-312">The location header URI can be tested in the browser.</span></span> <span data-ttu-id="89fb3-313">Skopiuj i wklej identyfikator URI nagłówka lokalizacji do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="89fb3-313">Copy and paste the location header URI into the browser.</span></span>

<span data-ttu-id="89fb3-314">Aby przetestować w programie Poster:</span><span class="sxs-lookup"><span data-stu-id="89fb3-314">To test in Postman:</span></span>

* <span data-ttu-id="89fb3-315">Wybierz kartę **nagłówki** w okienku **odpowiedź** .</span><span class="sxs-lookup"><span data-stu-id="89fb3-315">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="89fb3-316">Skopiuj wartość nagłówka **lokalizacji** :</span><span class="sxs-lookup"><span data-stu-id="89fb3-316">Copy the **Location** header value:</span></span>

  ![Karta nagłówki w konsoli programu Poster](first-web-api/_static/3/create.png)

* <span data-ttu-id="89fb3-318">Ustaw metodę HTTP na `GET` .</span><span class="sxs-lookup"><span data-stu-id="89fb3-318">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="89fb3-319">Ustaw identyfikator URI na `https://localhost:<port>/api/TodoItems/1` .</span><span class="sxs-lookup"><span data-stu-id="89fb3-319">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="89fb3-320">Na przykład `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="89fb3-320">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="89fb3-321">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-321">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="89fb3-322">Badanie metod GET</span><span class="sxs-lookup"><span data-stu-id="89fb3-322">Examine the GET methods</span></span>

<span data-ttu-id="89fb3-323">Zaimplementowane są dwa punkty końcowe GET:</span><span class="sxs-lookup"><span data-stu-id="89fb3-323">Two GET endpoints are implemented:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="89fb3-324">Przetestuj aplikację, wywołując dwa punkty końcowe z przeglądarki lub wpisu.</span><span class="sxs-lookup"><span data-stu-id="89fb3-324">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="89fb3-325">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="89fb3-325">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="89fb3-326">Odpowiedź podobna do poniższego jest generowana przez wywołanie `GetTodoItems` :</span><span class="sxs-lookup"><span data-stu-id="89fb3-326">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="89fb3-327">Test get przy użyciu programu Poster</span><span class="sxs-lookup"><span data-stu-id="89fb3-327">Test Get with Postman</span></span>

* <span data-ttu-id="89fb3-328">Utwórz nowe żądanie.</span><span class="sxs-lookup"><span data-stu-id="89fb3-328">Create a new request.</span></span>
* <span data-ttu-id="89fb3-329">Ustaw metodę HTTP, aby **uzyskać**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-329">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="89fb3-330">Ustaw identyfikator URI żądania na `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="89fb3-330">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="89fb3-331">Na przykład `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="89fb3-331">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="89fb3-332">Ustaw **dwa widoki okienka** w programie Poster.</span><span class="sxs-lookup"><span data-stu-id="89fb3-332">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="89fb3-333">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-333">Select **Send**.</span></span>

<span data-ttu-id="89fb3-334">Ta aplikacja używa bazy danych w pamięci.</span><span class="sxs-lookup"><span data-stu-id="89fb3-334">This app uses an in-memory database.</span></span> <span data-ttu-id="89fb3-335">Jeśli aplikacja zostanie zatrzymana i uruchomiona, poprzednie żądanie GET nie zwróci żadnych danych.</span><span class="sxs-lookup"><span data-stu-id="89fb3-335">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="89fb3-336">Jeśli nie zostaną zwrócone żadne dane, [Opublikuj](#post) dane w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="89fb3-336">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="89fb3-337">Ścieżki routingu i adresów URL</span><span class="sxs-lookup"><span data-stu-id="89fb3-337">Routing and URL paths</span></span>

<span data-ttu-id="89fb3-338">Ten [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) atrybut oznacza metodę, która reaguje na żądanie HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="89fb3-338">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="89fb3-339">Ścieżka adresu URL dla każdej metody jest zbudowana w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="89fb3-339">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="89fb3-340">Zacznij od ciągu szablonu w `Route` atrybucie kontrolera:</span><span class="sxs-lookup"><span data-stu-id="89fb3-340">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="89fb3-341">Zastąp `[controller]` nazwą kontrolera, którą Konwencją jest nazwa klasy kontrolera minus sufiks "Controller".</span><span class="sxs-lookup"><span data-stu-id="89fb3-341">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="89fb3-342">Dla tego przykładu nazwa klasy kontrolera to **TodoItems**Controller, więc nazwa kontrolera to "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="89fb3-342">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="89fb3-343">W ASP.NET Core [routingu](xref:mvc/controllers/routing) jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="89fb3-343">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="89fb3-344">Jeśli `[HttpGet]` atrybut ma szablon trasy (na przykład `[HttpGet("products")]` ), Dodaj go do ścieżki.</span><span class="sxs-lookup"><span data-stu-id="89fb3-344">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="89fb3-345">Ten przykład nie używa szablonu.</span><span class="sxs-lookup"><span data-stu-id="89fb3-345">This sample doesn't use a template.</span></span> <span data-ttu-id="89fb3-346">Aby uzyskać więcej informacji, zobacz temat [Routing atrybutów z atrybutami http [Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="89fb3-346">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="89fb3-347">W poniższej `GetTodoItem` metodzie `"{id}"` jest zmienną zastępczą dla unikatowego identyfikatora elementu do wykonania.</span><span class="sxs-lookup"><span data-stu-id="89fb3-347">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="89fb3-348">Gdy `GetTodoItem` jest wywoływana, wartość `"{id}"` w adresie URL jest podawana do metody w jej `id` parametrze.</span><span class="sxs-lookup"><span data-stu-id="89fb3-348">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="89fb3-349">Wartości zwracane</span><span class="sxs-lookup"><span data-stu-id="89fb3-349">Return values</span></span>

<span data-ttu-id="89fb3-350">Zwracany typ `GetTodoItems` `GetTodoItem` metod i jest [ \<T> typem ActionResult](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="89fb3-350">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="89fb3-351">ASP.NET Core automatycznie serializować obiektu do [formatu JSON](https://www.json.org/) i zapisuje kod JSON w treści komunikatu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="89fb3-351">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="89fb3-352">Kod odpowiedzi dla tego typu zwracanego to [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), przy założeniu, że nie istnieją Nieobsłużone wyjątki.</span><span class="sxs-lookup"><span data-stu-id="89fb3-352">The response code for this return type is [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="89fb3-353">Nieobsłużone wyjątki są tłumaczone na błędy 5xx.</span><span class="sxs-lookup"><span data-stu-id="89fb3-353">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="89fb3-354">`ActionResult` typy zwracane mogą reprezentować szeroką gamę kodów stanu HTTP.</span><span class="sxs-lookup"><span data-stu-id="89fb3-354">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="89fb3-355">Na przykład `GetTodoItem` może zwracać dwie różne wartości stanu:</span><span class="sxs-lookup"><span data-stu-id="89fb3-355">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="89fb3-356">Jeśli żaden element nie jest zgodny z żądanym IDENTYFIKATORem, metoda zwraca kod błędu [stanu 404](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> .</span><span class="sxs-lookup"><span data-stu-id="89fb3-356">If no item matches the requested ID, the method returns a [404 status](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="89fb3-357">W przeciwnym razie metoda zwraca 200 z treścią odpowiedzi JSON.</span><span class="sxs-lookup"><span data-stu-id="89fb3-357">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="89fb3-358">Zwracanie `item` wyników w odpowiedzi HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="89fb3-358">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="89fb3-359">Metoda PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="89fb3-359">The PutTodoItem method</span></span>

<span data-ttu-id="89fb3-360">Przeanalizuj metodę `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="89fb3-360">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="89fb3-361">`PutTodoItem` jest podobny do `PostTodoItem` , z tą różnicą, że używa protokołu HTTP Put.</span><span class="sxs-lookup"><span data-stu-id="89fb3-361">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="89fb3-362">Odpowiedź to [204 (brak zawartości)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="89fb3-362">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="89fb3-363">Zgodnie ze specyfikacją protokołu HTTP żądanie PUT wymaga, aby klient wysłał całą zaktualizowaną jednostkę, a nie tylko te zmiany.</span><span class="sxs-lookup"><span data-stu-id="89fb3-363">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="89fb3-364">Aby zapewnić obsługę częściowych aktualizacji, użyj [poprawki http](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="89fb3-364">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="89fb3-365">Jeśli wystąpi błąd podczas wywoływania `PutTodoItem` , wywołaj, `GET` Aby upewnić się, że w bazie danych znajduje się element.</span><span class="sxs-lookup"><span data-stu-id="89fb3-365">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="89fb3-366">Testowanie metody PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="89fb3-366">Test the PutTodoItem method</span></span>

<span data-ttu-id="89fb3-367">Ten przykład korzysta z bazy danych w pamięci, która musi zostać zainicjowana za każdym razem, gdy aplikacja zostanie uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="89fb3-367">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="89fb3-368">Przed wykonaniem wywołania PUT musi istnieć element w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="89fb3-368">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="89fb3-369">Przed wykonaniem wywołania PUT należy wywołać metodę GET, aby upewnić się, że istnieje element w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="89fb3-369">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="89fb3-370">Zaktualizuj element do wykonania o identyfikatorze 1 i ustaw jego nazwę na `"feed fish"` :</span><span class="sxs-lookup"><span data-stu-id="89fb3-370">Update the to-do item that has Id = 1 and set its name to `"feed fish"`:</span></span>

```json
  {
    "Id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="89fb3-371">Na poniższej ilustracji przedstawiono aktualizację programu Poster:</span><span class="sxs-lookup"><span data-stu-id="89fb3-371">The following image shows the Postman update:</span></span>

![Konsola programu Poster pokazująca odpowiedź 204 (brak zawartości)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="89fb3-373">Metoda DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="89fb3-373">The DeleteTodoItem method</span></span>

<span data-ttu-id="89fb3-374">Przeanalizuj metodę `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="89fb3-374">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="89fb3-375">Testowanie metody DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="89fb3-375">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="89fb3-376">Użyj programu Poster, aby usunąć element do wykonania:</span><span class="sxs-lookup"><span data-stu-id="89fb3-376">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="89fb3-377">Ustaw metodę na `DELETE` .</span><span class="sxs-lookup"><span data-stu-id="89fb3-377">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="89fb3-378">Ustaw identyfikator URI obiektu do usunięcia (na przykład `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="89fb3-378">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="89fb3-379">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-379">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="89fb3-380">Zapobiegaj za pośrednictwem księgowania</span><span class="sxs-lookup"><span data-stu-id="89fb3-380">Prevent over-posting</span></span>

<span data-ttu-id="89fb3-381">Obecnie Przykładowa aplikacja uwidacznia cały `TodoItem` obiekt.</span><span class="sxs-lookup"><span data-stu-id="89fb3-381">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="89fb3-382">Aplikacje produkcyjne zwykle ograniczają dane wejściowe i zwracane przy użyciu podzestawu modelu.</span><span class="sxs-lookup"><span data-stu-id="89fb3-382">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="89fb3-383">Istnieje wiele powodów związanych z tym, a zabezpieczenia są głównymi.</span><span class="sxs-lookup"><span data-stu-id="89fb3-383">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="89fb3-384">Podzestaw modelu jest zwykle określany jako obiekt Transfer danych (DTO), model wejściowy lub model widoku.</span><span class="sxs-lookup"><span data-stu-id="89fb3-384">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="89fb3-385">**DTO** jest używany w tym artykule.</span><span class="sxs-lookup"><span data-stu-id="89fb3-385">**DTO** is used in this article.</span></span>

<span data-ttu-id="89fb3-386">DTO może służyć do:</span><span class="sxs-lookup"><span data-stu-id="89fb3-386">A DTO may be used to:</span></span>

* <span data-ttu-id="89fb3-387">Zablokuj nadmierne księgowanie.</span><span class="sxs-lookup"><span data-stu-id="89fb3-387">Prevent over-posting.</span></span>
* <span data-ttu-id="89fb3-388">Ukryj właściwości, które nie powinny być wyświetlane dla klientów.</span><span class="sxs-lookup"><span data-stu-id="89fb3-388">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="89fb3-389">Pomiń niektóre właściwości, aby zmniejszyć rozmiar ładunku.</span><span class="sxs-lookup"><span data-stu-id="89fb3-389">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="89fb3-390">Spłaszcz wykresy obiektów zawierające obiekty zagnieżdżone.</span><span class="sxs-lookup"><span data-stu-id="89fb3-390">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="89fb3-391">Spłaszczone wykresy obiektów mogą być wygodniejsze dla klientów.</span><span class="sxs-lookup"><span data-stu-id="89fb3-391">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="89fb3-392">Aby zademonstrować podejście DTO, zaktualizuj `TodoItem` klasę w celu uwzględnienia pola tajnego:</span><span class="sxs-lookup"><span data-stu-id="89fb3-392">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="89fb3-393">Pole tajne musi być ukryte w tej aplikacji, ale aplikacja administracyjna mogła ją uwidocznić.</span><span class="sxs-lookup"><span data-stu-id="89fb3-393">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="89fb3-394">Sprawdź, czy można opublikować i pobrać pole tajne.</span><span class="sxs-lookup"><span data-stu-id="89fb3-394">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="89fb3-395">Utwórz model DTO:</span><span class="sxs-lookup"><span data-stu-id="89fb3-395">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="89fb3-396">Zaktualizuj, `TodoItemsController` Aby użyć `TodoItemDTO` :</span><span class="sxs-lookup"><span data-stu-id="89fb3-396">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="89fb3-397">Upewnij się, że nie można opublikować lub pobrać pola tajnego.</span><span class="sxs-lookup"><span data-stu-id="89fb3-397">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="89fb3-398">Wywoływanie interfejsu API sieci Web przy użyciu języka JavaScript</span><span class="sxs-lookup"><span data-stu-id="89fb3-398">Call the web API with JavaScript</span></span>

<span data-ttu-id="89fb3-399">Zobacz [Samouczek: wywoływanie interfejsu API sieci web ASP.NET Core przy użyciu języka JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="89fb3-399">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="89fb3-400">Ten samouczek zawiera informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="89fb3-400">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="89fb3-401">Utwórz projekt interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="89fb3-401">Create a web API project.</span></span>
> * <span data-ttu-id="89fb3-402">Dodaj klasę modelu i kontekst bazy danych.</span><span class="sxs-lookup"><span data-stu-id="89fb3-402">Add a model class and a database context.</span></span>
> * <span data-ttu-id="89fb3-403">Tworzy szkielet kontrolera z metodami CRUD.</span><span class="sxs-lookup"><span data-stu-id="89fb3-403">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="89fb3-404">Skonfiguruj Routing, ścieżki URL i wartości zwracane.</span><span class="sxs-lookup"><span data-stu-id="89fb3-404">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="89fb3-405">Wywołaj interfejs API sieci Web za pomocą programu Poster.</span><span class="sxs-lookup"><span data-stu-id="89fb3-405">Call the web API with Postman.</span></span>

<span data-ttu-id="89fb3-406">Na końcu znajduje się internetowy interfejs API, który może zarządzać elementami do wykonania przechowywanymi w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="89fb3-406">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="89fb3-407">Omówienie</span><span class="sxs-lookup"><span data-stu-id="89fb3-407">Overview</span></span>

<span data-ttu-id="89fb3-408">Ten samouczek tworzy następujący interfejs API:</span><span class="sxs-lookup"><span data-stu-id="89fb3-408">This tutorial creates the following API:</span></span>

|<span data-ttu-id="89fb3-409">Interfejs API</span><span class="sxs-lookup"><span data-stu-id="89fb3-409">API</span></span> | <span data-ttu-id="89fb3-410">Opis</span><span class="sxs-lookup"><span data-stu-id="89fb3-410">Description</span></span> | <span data-ttu-id="89fb3-411">Treść żądania</span><span class="sxs-lookup"><span data-stu-id="89fb3-411">Request body</span></span> | <span data-ttu-id="89fb3-412">Treść odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="89fb3-412">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="89fb3-413">Pobierz wszystkie elementy do wykonania</span><span class="sxs-lookup"><span data-stu-id="89fb3-413">Get all to-do items</span></span> | <span data-ttu-id="89fb3-414">Brak</span><span class="sxs-lookup"><span data-stu-id="89fb3-414">None</span></span> | <span data-ttu-id="89fb3-415">Tablica elementów do wykonania</span><span class="sxs-lookup"><span data-stu-id="89fb3-415">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="89fb3-416">Pobieranie elementu według identyfikatora</span><span class="sxs-lookup"><span data-stu-id="89fb3-416">Get an item by ID</span></span> | <span data-ttu-id="89fb3-417">Brak</span><span class="sxs-lookup"><span data-stu-id="89fb3-417">None</span></span> | <span data-ttu-id="89fb3-418">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="89fb3-418">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="89fb3-419">Dodaj nowy element</span><span class="sxs-lookup"><span data-stu-id="89fb3-419">Add a new item</span></span> | <span data-ttu-id="89fb3-420">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="89fb3-420">To-do item</span></span> | <span data-ttu-id="89fb3-421">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="89fb3-421">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="89fb3-422">Aktualizowanie istniejącego elementu &nbsp;</span><span class="sxs-lookup"><span data-stu-id="89fb3-422">Update an existing item &nbsp;</span></span> | <span data-ttu-id="89fb3-423">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="89fb3-423">To-do item</span></span> | <span data-ttu-id="89fb3-424">Brak</span><span class="sxs-lookup"><span data-stu-id="89fb3-424">None</span></span> |
|<span data-ttu-id="89fb3-425">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="89fb3-425">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="89fb3-426">Usuń element &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="89fb3-426">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="89fb3-427">Brak</span><span class="sxs-lookup"><span data-stu-id="89fb3-427">None</span></span> | <span data-ttu-id="89fb3-428">Brak</span><span class="sxs-lookup"><span data-stu-id="89fb3-428">None</span></span>|

<span data-ttu-id="89fb3-429">Na poniższym diagramie przedstawiono projekt aplikacji.</span><span class="sxs-lookup"><span data-stu-id="89fb3-429">The following diagram shows the design of the app.</span></span>

![Klient jest reprezentowany przez pole po lewej stronie.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="89fb3-435">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="89fb3-435">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="89fb3-436">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="89fb3-436">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="89fb3-437">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="89fb3-437">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="89fb3-438">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="89fb3-438">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="89fb3-439">Tworzenie projektu sieci Web</span><span class="sxs-lookup"><span data-stu-id="89fb3-439">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="89fb3-440">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="89fb3-440">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="89fb3-441">Z menu **plik** wybierz pozycję **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-441">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="89fb3-442">Wybierz szablon **aplikacja sieci Web ASP.NET Core** a następnie kliknij przycisk **dalej**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-442">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="89fb3-443">Nazwij projekt *TodoApi* i kliknij pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-443">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="89fb3-444">W oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** upewnij się, że wybrano opcję **.net Core** i **ASP.NET Core 3,1** .</span><span class="sxs-lookup"><span data-stu-id="89fb3-444">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="89fb3-445">Wybierz szablon **interfejsu API** i kliknij przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-445">Select the **API** template and click **Create**.</span></span>

![Okno dialogowe programu VS New Project](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="89fb3-447">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="89fb3-447">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="89fb3-448">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="89fb3-448">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="89fb3-449">Zmień katalog ( `cd` ) do folderu, który będzie zawierać folder projektu.</span><span class="sxs-lookup"><span data-stu-id="89fb3-449">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="89fb3-450">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="89fb3-450">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="89fb3-451">Gdy zostanie wyświetlone okno dialogowe z pytaniem, czy chcesz dodać wymagane zasoby do projektu, wybierz opcję **tak**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-451">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="89fb3-452">Poprzednie polecenia:</span><span class="sxs-lookup"><span data-stu-id="89fb3-452">The preceding commands:</span></span>

  * <span data-ttu-id="89fb3-453">Tworzy nowy projekt internetowego interfejsu API i otwiera go w Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="89fb3-453">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="89fb3-454">Dodaje pakiety NuGet, które są wymagane w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="89fb3-454">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="89fb3-455">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="89fb3-455">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="89fb3-456">Wybierz pozycję **plik** > **nowe rozwiązanie**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-456">Select **File** > **New Solution**.</span></span>

  ![macOS nowe rozwiązanie](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="89fb3-458">W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz **.NET Core**pozycję  >  **App**  >  **interfejs API**aplikacji .NET Core  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-458">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="89fb3-459">W wersji 8,6 lub nowszej wybierz pozycję **Web and Console**  >  **App**  >  **interfejs API**  >  **Next**aplikacji sieci Web i konsoli.</span><span class="sxs-lookup"><span data-stu-id="89fb3-459">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![Wybór szablonu interfejsu API macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="89fb3-461">W oknie dialogowym **Konfigurowanie nowego interfejsu API sieci Web ASP.NET Core** wybierz najnowszą platformę **docelową**.NET Core 3. x.</span><span class="sxs-lookup"><span data-stu-id="89fb3-461">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="89fb3-462">Wybierz opcję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-462">Select **Next**.</span></span>

* <span data-ttu-id="89fb3-463">Wprowadź *TodoApi* jako **nazwę projektu** , a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-463">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![okno dialogowe konfiguracji](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="89fb3-465">Otwórz Terminal poleceń w folderze projektu i uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="89fb3-465">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="89fb3-466">Testowanie interfejsu API</span><span class="sxs-lookup"><span data-stu-id="89fb3-466">Test the API</span></span>

<span data-ttu-id="89fb3-467">Szablon projektu tworzy `WeatherForecast` interfejs API.</span><span class="sxs-lookup"><span data-stu-id="89fb3-467">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="89fb3-468">Wywołaj `Get` metodę z przeglądarki, aby przetestować aplikację.</span><span class="sxs-lookup"><span data-stu-id="89fb3-468">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="89fb3-469">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="89fb3-469">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="89fb3-470">Naciśnij klawisze CTRL + F5, aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="89fb3-470">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="89fb3-471">Program Visual Studio uruchamia przeglądarkę i przechodzi do `https://localhost:<port>/WeatherForecast` lokalizacji, gdzie `<port>` jest losowo wybierany numer portu.</span><span class="sxs-lookup"><span data-stu-id="89fb3-471">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="89fb3-472">Jeśli zostanie wyświetlone okno dialogowe z pytaniem, czy należy zaufać certyfikatowi IIS Express, wybierz pozycję **tak**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-472">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="89fb3-473">W wyświetlonym oknie dialogowym **ostrzeżenia o zabezpieczeniach** wybierz pozycję **tak**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-473">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="89fb3-474">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="89fb3-474">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="89fb3-475">Naciśnij klawisze CTRL + F5, aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="89fb3-475">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="89fb3-476">W przeglądarce przejdź do następującego adresu URL: `https://localhost:5001/WeatherForecast` .</span><span class="sxs-lookup"><span data-stu-id="89fb3-476">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="89fb3-477">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="89fb3-477">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="89fb3-478">Wybierz pozycję **Uruchom**  >  **Rozpocznij debugowanie** , aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="89fb3-478">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="89fb3-479">Visual Studio dla komputerów Mac uruchamia przeglądarkę i nawiguje do `https://localhost:<port>` , gdzie `<port>` jest losowo wybranym numerem portu.</span><span class="sxs-lookup"><span data-stu-id="89fb3-479">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="89fb3-480">Zwracany jest błąd HTTP 404 (nie znaleziono).</span><span class="sxs-lookup"><span data-stu-id="89fb3-480">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="89fb3-481">Dołącz `/WeatherForecast` do adresu URL (Zmień adres URL na `https://localhost:<port>/WeatherForecast` ).</span><span class="sxs-lookup"><span data-stu-id="89fb3-481">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="89fb3-482">Zwracany jest kod JSON podobny do następującego:</span><span class="sxs-lookup"><span data-stu-id="89fb3-482">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="89fb3-483">Dodaj klasę modelu</span><span class="sxs-lookup"><span data-stu-id="89fb3-483">Add a model class</span></span>

<span data-ttu-id="89fb3-484">*Model* to zestaw klas, które reprezentują dane zarządzane przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="89fb3-484">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="89fb3-485">Model tej aplikacji jest pojedynczą `TodoItem` klasą.</span><span class="sxs-lookup"><span data-stu-id="89fb3-485">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="89fb3-486">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="89fb3-486">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="89fb3-487">W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt.</span><span class="sxs-lookup"><span data-stu-id="89fb3-487">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="89fb3-488">Wybierz pozycję **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-488">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="89fb3-489">Nazwij folder *Models* .</span><span class="sxs-lookup"><span data-stu-id="89fb3-489">Name the folder *Models*.</span></span>

* <span data-ttu-id="89fb3-490">Kliknij prawym przyciskiem myszy *Models* folder i wybierz polecenie **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-490">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="89fb3-491">Nadaj klasie nazwę *TodoItem* i wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-491">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="89fb3-492">Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="89fb3-492">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="89fb3-493">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="89fb3-493">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="89fb3-494">Dodaj folder o nazwie *Models* .</span><span class="sxs-lookup"><span data-stu-id="89fb3-494">Add a folder named *Models*.</span></span>

* <span data-ttu-id="89fb3-495">Dodaj `TodoItem` do *Models* folderu klasę o następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="89fb3-495">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="89fb3-496">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="89fb3-496">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="89fb3-497">Kliknij prawym przyciskiem myszy projekt.</span><span class="sxs-lookup"><span data-stu-id="89fb3-497">Right-click the project.</span></span> <span data-ttu-id="89fb3-498">Wybierz pozycję **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-498">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="89fb3-499">Nazwij folder *Models* .</span><span class="sxs-lookup"><span data-stu-id="89fb3-499">Name the folder *Models*.</span></span>

  ![Nowy folder](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="89fb3-501">Kliknij prawym przyciskiem myszy *Models* folder, a następnie wybierz pozycję **Dodaj** > **nowy plik** > **ogólna** > **pusta Klasa**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-501">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="89fb3-502">Nazwij klasę *TodoItem*, a następnie kliknij pozycję **New (nowy**).</span><span class="sxs-lookup"><span data-stu-id="89fb3-502">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="89fb3-503">Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="89fb3-503">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="89fb3-504">`Id`Właściwość działa jako unikatowy klucz w relacyjnej bazie danych.</span><span class="sxs-lookup"><span data-stu-id="89fb3-504">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="89fb3-505">Klasy modelu mogą przejść do dowolnego miejsca w projekcie, ale *Models* folder jest używany przez Konwencję.</span><span class="sxs-lookup"><span data-stu-id="89fb3-505">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="89fb3-506">Dodawanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="89fb3-506">Add a database context</span></span>

<span data-ttu-id="89fb3-507">*Kontekst bazy danych* jest główną klasą, która koordynuje Entity Framework funkcji dla modelu danych.</span><span class="sxs-lookup"><span data-stu-id="89fb3-507">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="89fb3-508">Ta klasa jest tworzona przez wyprowadzanie z `Microsoft.EntityFrameworkCore.DbContext` klasy.</span><span class="sxs-lookup"><span data-stu-id="89fb3-508">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="89fb3-509">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="89fb3-509">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="89fb3-510">Dodawanie pakietów NuGet</span><span class="sxs-lookup"><span data-stu-id="89fb3-510">Add NuGet packages</span></span>

* <span data-ttu-id="89fb3-511">W menu **Narzędzia** wybierz pozycję **menedżer pakietów NuGet > zarządzanie pakietami NuGet dla rozwiązania**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-511">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="89fb3-512">Wybierz kartę **Przeglądaj** , a następnie w polu wyszukiwania wprowadź ciąg **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="89fb3-512">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="89fb3-513">W lewym okienku wybierz pozycję **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="89fb3-513">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="89fb3-514">Zaznacz pole wyboru **projekt** w prawym okienku, a następnie wybierz pozycję **Zainstaluj**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-514">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="89fb3-515">Użyj powyższych instrukcji, aby dodać pakiet NuGet **Microsoft. EntityFrameworkCore. inMemory** .</span><span class="sxs-lookup"><span data-stu-id="89fb3-515">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

![Menedżer pakietów NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="89fb3-517">Dodawanie kontekstu bazy danych TodoContext</span><span class="sxs-lookup"><span data-stu-id="89fb3-517">Add the TodoContext database context</span></span>

* <span data-ttu-id="89fb3-518">Kliknij prawym przyciskiem myszy *Models* folder i wybierz polecenie **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-518">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="89fb3-519">Nadaj klasie nazwę *TodoContext* i kliknij przycisk **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-519">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="89fb3-520">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="89fb3-520">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="89fb3-521">Dodaj `TodoContext` klasę do *Models* folderu.</span><span class="sxs-lookup"><span data-stu-id="89fb3-521">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="89fb3-522">Wprowadź następujący kod:</span><span class="sxs-lookup"><span data-stu-id="89fb3-522">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="89fb3-523">Rejestrowanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="89fb3-523">Register the database context</span></span>

<span data-ttu-id="89fb3-524">W ASP.NET Core usługi, takie jak kontekst bazy danych, muszą być zarejestrowane z kontenerem [iniekcji zależności (di)](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="89fb3-524">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="89fb3-525">Kontener udostępnia usługę kontrolerom.</span><span class="sxs-lookup"><span data-stu-id="89fb3-525">The container provides the service to controllers.</span></span>

<span data-ttu-id="89fb3-526">Zaktualizuj *Startup.cs* o następujący wyróżniony kod:</span><span class="sxs-lookup"><span data-stu-id="89fb3-526">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="89fb3-527">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="89fb3-527">The preceding code:</span></span>

* <span data-ttu-id="89fb3-528">Usuwa nieużywane `using` deklaracje.</span><span class="sxs-lookup"><span data-stu-id="89fb3-528">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="89fb3-529">Dodaje kontekst bazy danych do kontenera DI.</span><span class="sxs-lookup"><span data-stu-id="89fb3-529">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="89fb3-530">Określa, że kontekst bazy danych będzie używać bazy danych w pamięci.</span><span class="sxs-lookup"><span data-stu-id="89fb3-530">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="89fb3-531">Tworzenie szkieletu kontrolera</span><span class="sxs-lookup"><span data-stu-id="89fb3-531">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="89fb3-532">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="89fb3-532">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="89fb3-533">Kliknij prawym przyciskiem myszy folder *controllers* .</span><span class="sxs-lookup"><span data-stu-id="89fb3-533">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="89fb3-534">Wybierz pozycję **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-534">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="89fb3-535">Wybierz pozycję **kontroler interfejsu API z akcjami, używając Entity Framework**, a następnie wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-535">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="89fb3-536">Na stronie **Dodawanie kontrolera interfejsu API z akcjami przy użyciu Entity Framework** dialogowego:</span><span class="sxs-lookup"><span data-stu-id="89fb3-536">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="89fb3-537">Wybierz pozycję **TodoItem (TodoApi. Models )** w **klasie model**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-537">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="89fb3-538">Wybierz pozycję **TodoContext (TodoApi. Models )** w **klasie kontekstu danych**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-538">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="89fb3-539">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-539">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="89fb3-540">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="89fb3-540">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="89fb3-541">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="89fb3-541">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="89fb3-542">Poprzednie polecenia:</span><span class="sxs-lookup"><span data-stu-id="89fb3-542">The preceding commands:</span></span>

* <span data-ttu-id="89fb3-543">Dodaj pakiety NuGet wymagane do tworzenia szkieletów.</span><span class="sxs-lookup"><span data-stu-id="89fb3-543">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="89fb3-544">Instaluje aparat tworzenia szkieletu ( `dotnet-aspnet-codegenerator` ).</span><span class="sxs-lookup"><span data-stu-id="89fb3-544">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="89fb3-545">Szkielety `TodoItemsController` .</span><span class="sxs-lookup"><span data-stu-id="89fb3-545">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="89fb3-546">Wygenerowany kod:</span><span class="sxs-lookup"><span data-stu-id="89fb3-546">The generated code:</span></span>

* <span data-ttu-id="89fb3-547">Oznacza klasę [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atrybutem.</span><span class="sxs-lookup"><span data-stu-id="89fb3-547">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="89fb3-548">Ten atrybut wskazuje, że kontroler odpowiada na żądania interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="89fb3-548">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="89fb3-549">Aby uzyskać informacje o określonych zachowaniach, które włącza atrybut, zobacz <xref:web-api/index> .</span><span class="sxs-lookup"><span data-stu-id="89fb3-549">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="89fb3-550">Używa funkcji DI do iniekcji kontekstu bazy danych ( `TodoContext` ) do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="89fb3-550">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="89fb3-551">Kontekst bazy danych jest używany w każdej z metod [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) w kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="89fb3-551">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="89fb3-552">Szablony ASP.NET Core dla:</span><span class="sxs-lookup"><span data-stu-id="89fb3-552">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="89fb3-553">Kontrolery z widokami obejmują `[action]` szablon trasy.</span><span class="sxs-lookup"><span data-stu-id="89fb3-553">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="89fb3-554">Kontrolery interfejsu API nie należą `[action]` do szablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="89fb3-554">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="89fb3-555">Gdy `[action]` token nie znajduje się w szablonie trasy, nazwa [akcji](xref:mvc/controllers/routing#action) jest wykluczona z trasy.</span><span class="sxs-lookup"><span data-stu-id="89fb3-555">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="89fb3-556">Oznacza to, że nazwa metody skojarzonej z akcją nie jest używana w zgodnej trasie.</span><span class="sxs-lookup"><span data-stu-id="89fb3-556">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="89fb3-557">Badanie metody PostTodoItem Create</span><span class="sxs-lookup"><span data-stu-id="89fb3-557">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="89fb3-558">Zastąp instrukcję return w, `PostTodoItem` Aby użyć operatora [nameof](/dotnet/csharp/language-reference/operators/nameof) :</span><span class="sxs-lookup"><span data-stu-id="89fb3-558">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="89fb3-559">Poprzedni kod jest metodą POST protokołu HTTP, jak wskazano w [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) atrybucie.</span><span class="sxs-lookup"><span data-stu-id="89fb3-559">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="89fb3-560">Metoda pobiera wartość elementu do wykonania z treści żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="89fb3-560">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="89fb3-561">Aby uzyskać więcej informacji, zobacz temat [Routing atrybutów z atrybutami http [Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="89fb3-561">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="89fb3-562"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>Metoda:</span><span class="sxs-lookup"><span data-stu-id="89fb3-562">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="89fb3-563">W razie powodzenia zwraca kod stanu HTTP 201.</span><span class="sxs-lookup"><span data-stu-id="89fb3-563">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="89fb3-564">HTTP 201 to standardowa odpowiedź dla metody POST protokołu HTTP, która tworzy nowy zasób na serwerze.</span><span class="sxs-lookup"><span data-stu-id="89fb3-564">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="89fb3-565">Dodaje nagłówek [lokalizacji](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) do odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="89fb3-565">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="89fb3-566">`Location`Nagłówek określa [Identyfikator URI](https://developer.mozilla.org/docs/Glossary/URI) nowo utworzonego elementu do wykonania.</span><span class="sxs-lookup"><span data-stu-id="89fb3-566">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="89fb3-567">Aby uzyskać więcej informacji, zobacz [10.2.2 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="89fb3-567">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="89fb3-568">Odwołuje się do `GetTodoItem` akcji tworzenia `Location` identyfikatora URI nagłówka.</span><span class="sxs-lookup"><span data-stu-id="89fb3-568">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="89fb3-569">`nameof`Słowo kluczowe języka C# służy do zapobiegania twardemu kodowaniu nazwy akcji w `CreatedAtAction` wywołaniu.</span><span class="sxs-lookup"><span data-stu-id="89fb3-569">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="89fb3-570">Zainstaluj program Poster</span><span class="sxs-lookup"><span data-stu-id="89fb3-570">Install Postman</span></span>

<span data-ttu-id="89fb3-571">Ten samouczek używa programu do testowania interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="89fb3-571">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="89fb3-572">Zainstaluj program [Poster](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="89fb3-572">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="89fb3-573">Uruchom aplikację internetową.</span><span class="sxs-lookup"><span data-stu-id="89fb3-573">Start the web app.</span></span>
* <span data-ttu-id="89fb3-574">Uruchom wpis.</span><span class="sxs-lookup"><span data-stu-id="89fb3-574">Start Postman.</span></span>
* <span data-ttu-id="89fb3-575">Wyłącz **weryfikację certyfikatu SSL**</span><span class="sxs-lookup"><span data-stu-id="89fb3-575">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="89fb3-576">W **File** obszarze > **Ustawienia** pliku (karta**Ogólne** ) Wyłącz **weryfikację certyfikatu SSL**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-576">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="89fb3-577">Po przetestowaniu kontrolera ponownie Włącz weryfikację certyfikatu SSL.</span><span class="sxs-lookup"><span data-stu-id="89fb3-577">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="89fb3-578">Test PostTodoItem za pomocą programu Poster</span><span class="sxs-lookup"><span data-stu-id="89fb3-578">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="89fb3-579">Utwórz nowe żądanie.</span><span class="sxs-lookup"><span data-stu-id="89fb3-579">Create a new request.</span></span>
* <span data-ttu-id="89fb3-580">Ustaw metodę HTTP na `POST` .</span><span class="sxs-lookup"><span data-stu-id="89fb3-580">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="89fb3-581">Ustaw identyfikator URI na `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="89fb3-581">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="89fb3-582">Na przykład `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="89fb3-582">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="89fb3-583">Wybierz kartę **Treść**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-583">Select the **Body** tab.</span></span>
* <span data-ttu-id="89fb3-584">Wybierz przycisk radiowy **RAW** .</span><span class="sxs-lookup"><span data-stu-id="89fb3-584">Select the **raw** radio button.</span></span>
* <span data-ttu-id="89fb3-585">Ustaw typ na **JSON (Application/JSON)**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-585">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="89fb3-586">W treści żądania wprowadź kod JSON dla elementu do wykonania:</span><span class="sxs-lookup"><span data-stu-id="89fb3-586">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="89fb3-587">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-587">Select **Send**.</span></span>

  ![Ogłoś przy użyciu żądania Create](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a><span data-ttu-id="89fb3-589">Testowanie identyfikatora URI nagłówka lokalizacji za pomocą programu Poster</span><span class="sxs-lookup"><span data-stu-id="89fb3-589">Test the location header URI with Postman</span></span>

* <span data-ttu-id="89fb3-590">Wybierz kartę **nagłówki** w okienku **odpowiedź** .</span><span class="sxs-lookup"><span data-stu-id="89fb3-590">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="89fb3-591">Skopiuj wartość nagłówka **lokalizacji** :</span><span class="sxs-lookup"><span data-stu-id="89fb3-591">Copy the **Location** header value:</span></span>

  ![Karta nagłówki w konsoli programu Poster](first-web-api/_static/3/create.png)

* <span data-ttu-id="89fb3-593">Ustaw metodę HTTP na `GET` .</span><span class="sxs-lookup"><span data-stu-id="89fb3-593">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="89fb3-594">Ustaw identyfikator URI na `https://localhost:<port>/api/TodoItems/1` .</span><span class="sxs-lookup"><span data-stu-id="89fb3-594">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="89fb3-595">Na przykład `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="89fb3-595">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="89fb3-596">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-596">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="89fb3-597">Badanie metod GET</span><span class="sxs-lookup"><span data-stu-id="89fb3-597">Examine the GET methods</span></span>

<span data-ttu-id="89fb3-598">Te metody implementują dwa punkty końcowe GET:</span><span class="sxs-lookup"><span data-stu-id="89fb3-598">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="89fb3-599">Przetestuj aplikację, wywołując dwa punkty końcowe z przeglądarki lub wpisu.</span><span class="sxs-lookup"><span data-stu-id="89fb3-599">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="89fb3-600">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="89fb3-600">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="89fb3-601">Odpowiedź podobna do poniższego jest generowana przez wywołanie `GetTodoItems` :</span><span class="sxs-lookup"><span data-stu-id="89fb3-601">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="89fb3-602">Test get przy użyciu programu Poster</span><span class="sxs-lookup"><span data-stu-id="89fb3-602">Test Get with Postman</span></span>

* <span data-ttu-id="89fb3-603">Utwórz nowe żądanie.</span><span class="sxs-lookup"><span data-stu-id="89fb3-603">Create a new request.</span></span>
* <span data-ttu-id="89fb3-604">Ustaw metodę HTTP, aby **uzyskać**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-604">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="89fb3-605">Ustaw identyfikator URI żądania na `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="89fb3-605">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="89fb3-606">Na przykład `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="89fb3-606">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="89fb3-607">Ustaw **dwa widoki okienka** w programie Poster.</span><span class="sxs-lookup"><span data-stu-id="89fb3-607">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="89fb3-608">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-608">Select **Send**.</span></span>

<span data-ttu-id="89fb3-609">Ta aplikacja używa bazy danych w pamięci.</span><span class="sxs-lookup"><span data-stu-id="89fb3-609">This app uses an in-memory database.</span></span> <span data-ttu-id="89fb3-610">Jeśli aplikacja zostanie zatrzymana i uruchomiona, poprzednie żądanie GET nie zwróci żadnych danych.</span><span class="sxs-lookup"><span data-stu-id="89fb3-610">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="89fb3-611">Jeśli nie zostaną zwrócone żadne dane, [Opublikuj](#post) dane w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="89fb3-611">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="89fb3-612">Ścieżki routingu i adresów URL</span><span class="sxs-lookup"><span data-stu-id="89fb3-612">Routing and URL paths</span></span>

<span data-ttu-id="89fb3-613">Ten [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) atrybut oznacza metodę, która reaguje na żądanie HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="89fb3-613">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="89fb3-614">Ścieżka adresu URL dla każdej metody jest zbudowana w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="89fb3-614">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="89fb3-615">Zacznij od ciągu szablonu w `Route` atrybucie kontrolera:</span><span class="sxs-lookup"><span data-stu-id="89fb3-615">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="89fb3-616">Zastąp `[controller]` nazwą kontrolera, którą Konwencją jest nazwa klasy kontrolera minus sufiks "Controller".</span><span class="sxs-lookup"><span data-stu-id="89fb3-616">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="89fb3-617">Dla tego przykładu nazwa klasy kontrolera to **TodoItems**Controller, więc nazwa kontrolera to "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="89fb3-617">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="89fb3-618">W ASP.NET Core [routingu](xref:mvc/controllers/routing) jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="89fb3-618">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="89fb3-619">Jeśli `[HttpGet]` atrybut ma szablon trasy (na przykład `[HttpGet("products")]` ), Dodaj go do ścieżki.</span><span class="sxs-lookup"><span data-stu-id="89fb3-619">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="89fb3-620">Ten przykład nie używa szablonu.</span><span class="sxs-lookup"><span data-stu-id="89fb3-620">This sample doesn't use a template.</span></span> <span data-ttu-id="89fb3-621">Aby uzyskać więcej informacji, zobacz temat [Routing atrybutów z atrybutami http [Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="89fb3-621">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="89fb3-622">W poniższej `GetTodoItem` metodzie `"{id}"` jest zmienną zastępczą dla unikatowego identyfikatora elementu do wykonania.</span><span class="sxs-lookup"><span data-stu-id="89fb3-622">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="89fb3-623">Gdy `GetTodoItem` jest wywoływana, wartość `"{id}"` w adresie URL jest podawana do metody w jej `id` parametrze.</span><span class="sxs-lookup"><span data-stu-id="89fb3-623">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="89fb3-624">Wartości zwracane</span><span class="sxs-lookup"><span data-stu-id="89fb3-624">Return values</span></span> 

<span data-ttu-id="89fb3-625">Zwracany typ `GetTodoItems` `GetTodoItem` metod i jest [ \<T> typem ActionResult](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="89fb3-625">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="89fb3-626">ASP.NET Core automatycznie serializować obiektu do [formatu JSON](https://www.json.org/) i zapisuje kod JSON w treści komunikatu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="89fb3-626">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="89fb3-627">Kod odpowiedzi dla tego typu zwracanego to 200, przy założeniu, że nie istnieją Nieobsłużone wyjątki.</span><span class="sxs-lookup"><span data-stu-id="89fb3-627">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="89fb3-628">Nieobsłużone wyjątki są tłumaczone na błędy 5xx.</span><span class="sxs-lookup"><span data-stu-id="89fb3-628">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="89fb3-629">`ActionResult` typy zwracane mogą reprezentować szeroką gamę kodów stanu HTTP.</span><span class="sxs-lookup"><span data-stu-id="89fb3-629">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="89fb3-630">Na przykład `GetTodoItem` może zwracać dwie różne wartości stanu:</span><span class="sxs-lookup"><span data-stu-id="89fb3-630">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="89fb3-631">Jeśli żaden element nie jest zgodny z żądanym IDENTYFIKATORem, metoda zwróci <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> Kod błędu 404.</span><span class="sxs-lookup"><span data-stu-id="89fb3-631">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="89fb3-632">W przeciwnym razie metoda zwraca 200 z treścią odpowiedzi JSON.</span><span class="sxs-lookup"><span data-stu-id="89fb3-632">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="89fb3-633">Zwracanie `item` wyników w odpowiedzi HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="89fb3-633">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="89fb3-634">Metoda PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="89fb3-634">The PutTodoItem method</span></span>

<span data-ttu-id="89fb3-635">Przeanalizuj metodę `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="89fb3-635">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="89fb3-636">`PutTodoItem` jest podobny do `PostTodoItem` , z tą różnicą, że używa protokołu HTTP Put.</span><span class="sxs-lookup"><span data-stu-id="89fb3-636">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="89fb3-637">Odpowiedź to [204 (brak zawartości)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="89fb3-637">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="89fb3-638">Zgodnie ze specyfikacją protokołu HTTP żądanie PUT wymaga, aby klient wysłał całą zaktualizowaną jednostkę, a nie tylko te zmiany.</span><span class="sxs-lookup"><span data-stu-id="89fb3-638">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="89fb3-639">Aby zapewnić obsługę częściowych aktualizacji, użyj [poprawki http](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="89fb3-639">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="89fb3-640">Jeśli wystąpi błąd podczas wywoływania `PutTodoItem` , wywołaj, `GET` Aby upewnić się, że w bazie danych znajduje się element.</span><span class="sxs-lookup"><span data-stu-id="89fb3-640">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="89fb3-641">Testowanie metody PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="89fb3-641">Test the PutTodoItem method</span></span>

<span data-ttu-id="89fb3-642">Ten przykład korzysta z bazy danych w pamięci, która musi zostać zainicjowana za każdym razem, gdy aplikacja zostanie uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="89fb3-642">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="89fb3-643">Przed wykonaniem wywołania PUT musi istnieć element w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="89fb3-643">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="89fb3-644">Przed wykonaniem wywołania PUT należy wywołać metodę GET, aby upewnić się, że istnieje element w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="89fb3-644">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="89fb3-645">Zaktualizuj element do wykonania o identyfikatorze 1 i ustaw jego nazwę na "Źródło danych":</span><span class="sxs-lookup"><span data-stu-id="89fb3-645">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="89fb3-646">Na poniższej ilustracji przedstawiono aktualizację programu Poster:</span><span class="sxs-lookup"><span data-stu-id="89fb3-646">The following image shows the Postman update:</span></span>

![Konsola programu Poster pokazująca odpowiedź 204 (brak zawartości)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="89fb3-648">Metoda DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="89fb3-648">The DeleteTodoItem method</span></span>

<span data-ttu-id="89fb3-649">Przeanalizuj metodę `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="89fb3-649">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="89fb3-650">Testowanie metody DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="89fb3-650">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="89fb3-651">Użyj programu Poster, aby usunąć element do wykonania:</span><span class="sxs-lookup"><span data-stu-id="89fb3-651">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="89fb3-652">Ustaw metodę na `DELETE` .</span><span class="sxs-lookup"><span data-stu-id="89fb3-652">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="89fb3-653">Ustaw identyfikator URI obiektu do usunięcia (na przykład `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="89fb3-653">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="89fb3-654">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-654">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="89fb3-655">Zapobiegaj za pośrednictwem księgowania</span><span class="sxs-lookup"><span data-stu-id="89fb3-655">Prevent over-posting</span></span>

<span data-ttu-id="89fb3-656">Obecnie Przykładowa aplikacja uwidacznia cały `TodoItem` obiekt.</span><span class="sxs-lookup"><span data-stu-id="89fb3-656">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="89fb3-657">Aplikacje produkcyjne zwykle ograniczają dane wejściowe i zwracane przy użyciu podzestawu modelu.</span><span class="sxs-lookup"><span data-stu-id="89fb3-657">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="89fb3-658">Istnieje wiele powodów związanych z tym, a zabezpieczenia są głównymi.</span><span class="sxs-lookup"><span data-stu-id="89fb3-658">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="89fb3-659">Podzestaw modelu jest zwykle określany jako obiekt Transfer danych (DTO), model wejściowy lub model widoku.</span><span class="sxs-lookup"><span data-stu-id="89fb3-659">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="89fb3-660">**DTO** jest używany w tym artykule.</span><span class="sxs-lookup"><span data-stu-id="89fb3-660">**DTO** is used in this article.</span></span>

<span data-ttu-id="89fb3-661">DTO może służyć do:</span><span class="sxs-lookup"><span data-stu-id="89fb3-661">A DTO may be used to:</span></span>

* <span data-ttu-id="89fb3-662">Zablokuj nadmierne księgowanie.</span><span class="sxs-lookup"><span data-stu-id="89fb3-662">Prevent over-posting.</span></span>
* <span data-ttu-id="89fb3-663">Ukryj właściwości, które nie powinny być wyświetlane dla klientów.</span><span class="sxs-lookup"><span data-stu-id="89fb3-663">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="89fb3-664">Pomiń niektóre właściwości, aby zmniejszyć rozmiar ładunku.</span><span class="sxs-lookup"><span data-stu-id="89fb3-664">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="89fb3-665">Spłaszcz wykresy obiektów zawierające obiekty zagnieżdżone.</span><span class="sxs-lookup"><span data-stu-id="89fb3-665">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="89fb3-666">Spłaszczone wykresy obiektów mogą być wygodniejsze dla klientów.</span><span class="sxs-lookup"><span data-stu-id="89fb3-666">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="89fb3-667">Aby zademonstrować podejście DTO, zaktualizuj `TodoItem` klasę w celu uwzględnienia pola tajnego:</span><span class="sxs-lookup"><span data-stu-id="89fb3-667">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="89fb3-668">Pole tajne musi być ukryte w tej aplikacji, ale aplikacja administracyjna mogła ją uwidocznić.</span><span class="sxs-lookup"><span data-stu-id="89fb3-668">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="89fb3-669">Sprawdź, czy można opublikować i pobrać pole tajne.</span><span class="sxs-lookup"><span data-stu-id="89fb3-669">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="89fb3-670">Utwórz model DTO:</span><span class="sxs-lookup"><span data-stu-id="89fb3-670">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="89fb3-671">Zaktualizuj, `TodoItemsController` Aby użyć `TodoItemDTO` :</span><span class="sxs-lookup"><span data-stu-id="89fb3-671">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="89fb3-672">Upewnij się, że nie można opublikować lub pobrać pola tajnego.</span><span class="sxs-lookup"><span data-stu-id="89fb3-672">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="89fb3-673">Wywoływanie interfejsu API sieci Web przy użyciu języka JavaScript</span><span class="sxs-lookup"><span data-stu-id="89fb3-673">Call the web API with JavaScript</span></span>

<span data-ttu-id="89fb3-674">Zobacz [Samouczek: wywoływanie interfejsu API sieci web ASP.NET Core przy użyciu języka JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="89fb3-674">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="89fb3-675">Ten samouczek zawiera informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="89fb3-675">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="89fb3-676">Utwórz projekt interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="89fb3-676">Create a web API project.</span></span>
> * <span data-ttu-id="89fb3-677">Dodaj klasę modelu i kontekst bazy danych.</span><span class="sxs-lookup"><span data-stu-id="89fb3-677">Add a model class and a database context.</span></span>
> * <span data-ttu-id="89fb3-678">Dodaj kontroler.</span><span class="sxs-lookup"><span data-stu-id="89fb3-678">Add a controller.</span></span>
> * <span data-ttu-id="89fb3-679">Dodaj metody CRUD.</span><span class="sxs-lookup"><span data-stu-id="89fb3-679">Add CRUD methods.</span></span>
> * <span data-ttu-id="89fb3-680">Skonfiguruj ścieżki routingu i adresów URL.</span><span class="sxs-lookup"><span data-stu-id="89fb3-680">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="89fb3-681">Określ wartości zwracane.</span><span class="sxs-lookup"><span data-stu-id="89fb3-681">Specify return values.</span></span>
> * <span data-ttu-id="89fb3-682">Wywołaj interfejs API sieci Web za pomocą programu Poster.</span><span class="sxs-lookup"><span data-stu-id="89fb3-682">Call the web API with Postman.</span></span>
> * <span data-ttu-id="89fb3-683">Wywołaj interfejs API sieci Web za pomocą języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="89fb3-683">Call the web API with JavaScript.</span></span>

<span data-ttu-id="89fb3-684">Na końcu znajduje się internetowy interfejs API, który może zarządzać elementami do wykonania przechowywanymi w relacyjnej bazie danych.</span><span class="sxs-lookup"><span data-stu-id="89fb3-684">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview-21"></a><span data-ttu-id="89fb3-685">Przegląd 2,1</span><span class="sxs-lookup"><span data-stu-id="89fb3-685">Overview 2.1</span></span>

<span data-ttu-id="89fb3-686">Ten samouczek tworzy następujący interfejs API:</span><span class="sxs-lookup"><span data-stu-id="89fb3-686">This tutorial creates the following API:</span></span>

|<span data-ttu-id="89fb3-687">Interfejs API</span><span class="sxs-lookup"><span data-stu-id="89fb3-687">API</span></span> | <span data-ttu-id="89fb3-688">Opis</span><span class="sxs-lookup"><span data-stu-id="89fb3-688">Description</span></span> | <span data-ttu-id="89fb3-689">Treść żądania</span><span class="sxs-lookup"><span data-stu-id="89fb3-689">Request body</span></span> | <span data-ttu-id="89fb3-690">Treść odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="89fb3-690">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="89fb3-691">Pobierz/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="89fb3-691">GET /api/TodoItems</span></span> | <span data-ttu-id="89fb3-692">Pobierz wszystkie elementy do wykonania</span><span class="sxs-lookup"><span data-stu-id="89fb3-692">Get all to-do items</span></span> | <span data-ttu-id="89fb3-693">Brak</span><span class="sxs-lookup"><span data-stu-id="89fb3-693">None</span></span> | <span data-ttu-id="89fb3-694">Tablica elementów do wykonania</span><span class="sxs-lookup"><span data-stu-id="89fb3-694">Array of to-do items</span></span>|
|<span data-ttu-id="89fb3-695">Pobierz/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="89fb3-695">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="89fb3-696">Pobieranie elementu według identyfikatora</span><span class="sxs-lookup"><span data-stu-id="89fb3-696">Get an item by ID</span></span> | <span data-ttu-id="89fb3-697">Brak</span><span class="sxs-lookup"><span data-stu-id="89fb3-697">None</span></span> | <span data-ttu-id="89fb3-698">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="89fb3-698">To-do item</span></span>|
|<span data-ttu-id="89fb3-699">Opublikuj/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="89fb3-699">POST /api/TodoItems</span></span> | <span data-ttu-id="89fb3-700">Dodaj nowy element</span><span class="sxs-lookup"><span data-stu-id="89fb3-700">Add a new item</span></span> | <span data-ttu-id="89fb3-701">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="89fb3-701">To-do item</span></span> | <span data-ttu-id="89fb3-702">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="89fb3-702">To-do item</span></span> |
|<span data-ttu-id="89fb3-703">Umieść/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="89fb3-703">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="89fb3-704">Aktualizowanie istniejącego elementu &nbsp;</span><span class="sxs-lookup"><span data-stu-id="89fb3-704">Update an existing item &nbsp;</span></span> | <span data-ttu-id="89fb3-705">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="89fb3-705">To-do item</span></span> | <span data-ttu-id="89fb3-706">Brak</span><span class="sxs-lookup"><span data-stu-id="89fb3-706">None</span></span> |
|<span data-ttu-id="89fb3-707">Usuń/api/TodoItems/{id} &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="89fb3-707">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="89fb3-708">Usuń element &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="89fb3-708">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="89fb3-709">Brak</span><span class="sxs-lookup"><span data-stu-id="89fb3-709">None</span></span> | <span data-ttu-id="89fb3-710">Brak</span><span class="sxs-lookup"><span data-stu-id="89fb3-710">None</span></span>|

<span data-ttu-id="89fb3-711">Na poniższym diagramie przedstawiono projekt aplikacji.</span><span class="sxs-lookup"><span data-stu-id="89fb3-711">The following diagram shows the design of the app.</span></span>

![Klient jest reprezentowany przez pole po lewej stronie.](first-web-api/_static/architecture.png)

## <a name="prerequisites-21"></a><span data-ttu-id="89fb3-717">Wymagania wstępne 2,1</span><span class="sxs-lookup"><span data-stu-id="89fb3-717">Prerequisites 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="89fb3-718">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="89fb3-718">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="89fb3-719">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="89fb3-719">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="89fb3-720">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="89fb3-720">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project-21"></a><span data-ttu-id="89fb3-721">Tworzenie projektu sieci Web 2,1</span><span class="sxs-lookup"><span data-stu-id="89fb3-721">Create a web project 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="89fb3-722">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="89fb3-722">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="89fb3-723">Z menu **plik** wybierz pozycję **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-723">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="89fb3-724">Wybierz szablon **aplikacja sieci Web ASP.NET Core** a następnie kliknij przycisk **dalej**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-724">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="89fb3-725">Nazwij projekt *TodoApi* i kliknij pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-725">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="89fb3-726">W oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** upewnij się, że wybrano opcję **.net Core** i **ASP.NET Core 2,2** .</span><span class="sxs-lookup"><span data-stu-id="89fb3-726">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="89fb3-727">Wybierz szablon **interfejsu API** i kliknij przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-727">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="89fb3-728">**Nie** zaznaczaj opcji **Włącz obsługę platformy Docker**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-728">**Don't** select **Enable Docker Support**.</span></span>

![Okno dialogowe programu VS New Project](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="89fb3-730">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="89fb3-730">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="89fb3-731">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="89fb3-731">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="89fb3-732">Zmień katalog ( `cd` ) do folderu, który będzie zawierać folder projektu.</span><span class="sxs-lookup"><span data-stu-id="89fb3-732">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="89fb3-733">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="89fb3-733">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="89fb3-734">Te polecenia tworzą nowy projekt internetowego interfejsu API i otwierają nowe wystąpienie Visual Studio Code w nowym folderze projektu.</span><span class="sxs-lookup"><span data-stu-id="89fb3-734">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="89fb3-735">Gdy zostanie wyświetlone okno dialogowe z pytaniem, czy chcesz dodać wymagane zasoby do projektu, wybierz opcję **tak**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-735">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="89fb3-736">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="89fb3-736">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="89fb3-737">Wybierz pozycję **plik** > **nowe rozwiązanie**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-737">Select **File** > **New Solution**.</span></span>

  ![macOS nowe rozwiązanie](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="89fb3-739">W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz **.NET Core**pozycję  >  **App**  >  **interfejs API**aplikacji .NET Core  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-739">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="89fb3-740">W wersji 8,6 lub nowszej wybierz pozycję **Web and Console**  >  **App**  >  **interfejs API**  >  **Next**aplikacji sieci Web i konsoli.</span><span class="sxs-lookup"><span data-stu-id="89fb3-740">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="89fb3-741">W oknie dialogowym **Konfigurowanie nowego interfejsu API sieci Web ASP.NET Core** wybierz najnowszą platformę **docelową**programu .NET Core 2. x.</span><span class="sxs-lookup"><span data-stu-id="89fb3-741">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="89fb3-742">Wybierz opcję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-742">Select **Next**.</span></span>

* <span data-ttu-id="89fb3-743">Wprowadź *TodoApi* jako **nazwę projektu** , a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-743">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![okno dialogowe konfiguracji](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api-21"></a><span data-ttu-id="89fb3-745">Testowanie interfejsu API 2,1</span><span class="sxs-lookup"><span data-stu-id="89fb3-745">Test the API 2.1</span></span>

<span data-ttu-id="89fb3-746">Szablon projektu tworzy `values` interfejs API.</span><span class="sxs-lookup"><span data-stu-id="89fb3-746">The project template creates a `values` API.</span></span> <span data-ttu-id="89fb3-747">Wywołaj `Get` metodę z przeglądarki, aby przetestować aplikację.</span><span class="sxs-lookup"><span data-stu-id="89fb3-747">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="89fb3-748">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="89fb3-748">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="89fb3-749">Naciśnij klawisze CTRL + F5, aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="89fb3-749">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="89fb3-750">Program Visual Studio uruchamia przeglądarkę i przechodzi do `https://localhost:<port>/api/values` lokalizacji, gdzie `<port>` jest losowo wybierany numer portu.</span><span class="sxs-lookup"><span data-stu-id="89fb3-750">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="89fb3-751">Jeśli zostanie wyświetlone okno dialogowe z pytaniem, czy należy zaufać certyfikatowi IIS Express, wybierz pozycję **tak**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-751">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="89fb3-752">W wyświetlonym oknie dialogowym **ostrzeżenia o zabezpieczeniach** wybierz pozycję **tak**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-752">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="89fb3-753">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="89fb3-753">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="89fb3-754">Naciśnij klawisze CTRL + F5, aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="89fb3-754">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="89fb3-755">W przeglądarce przejdź do następującego adresu URL: `https://localhost:5001/api/values` .</span><span class="sxs-lookup"><span data-stu-id="89fb3-755">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="89fb3-756">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="89fb3-756">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="89fb3-757">Wybierz pozycję **Uruchom**  >  **Rozpocznij debugowanie** , aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="89fb3-757">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="89fb3-758">Visual Studio dla komputerów Mac uruchamia przeglądarkę i nawiguje do `https://localhost:<port>` , gdzie `<port>` jest losowo wybranym numerem portu.</span><span class="sxs-lookup"><span data-stu-id="89fb3-758">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="89fb3-759">Zwracany jest błąd HTTP 404 (nie znaleziono).</span><span class="sxs-lookup"><span data-stu-id="89fb3-759">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="89fb3-760">Dołącz `/api/values` do adresu URL (Zmień adres URL na `https://localhost:<port>/api/values` ).</span><span class="sxs-lookup"><span data-stu-id="89fb3-760">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="89fb3-761">Zostanie zwrócony następujący kod JSON:</span><span class="sxs-lookup"><span data-stu-id="89fb3-761">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class-21"></a><span data-ttu-id="89fb3-762">Dodaj klasę modelu 2,1</span><span class="sxs-lookup"><span data-stu-id="89fb3-762">Add a model class 2.1</span></span>

<span data-ttu-id="89fb3-763">*Model* to zestaw klas, które reprezentują dane zarządzane przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="89fb3-763">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="89fb3-764">Model tej aplikacji jest pojedynczą `TodoItem` klasą.</span><span class="sxs-lookup"><span data-stu-id="89fb3-764">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="89fb3-765">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="89fb3-765">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="89fb3-766">W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt.</span><span class="sxs-lookup"><span data-stu-id="89fb3-766">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="89fb3-767">Wybierz pozycję **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-767">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="89fb3-768">Nazwij folder *Models* .</span><span class="sxs-lookup"><span data-stu-id="89fb3-768">Name the folder *Models*.</span></span>

* <span data-ttu-id="89fb3-769">Kliknij prawym przyciskiem myszy *Models* folder i wybierz polecenie **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-769">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="89fb3-770">Nadaj klasie nazwę *TodoItem* i wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-770">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="89fb3-771">Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="89fb3-771">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="89fb3-772">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="89fb3-772">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="89fb3-773">Dodaj folder o nazwie *Models* .</span><span class="sxs-lookup"><span data-stu-id="89fb3-773">Add a folder named *Models*.</span></span>

* <span data-ttu-id="89fb3-774">Dodaj `TodoItem` do *Models* folderu klasę o następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="89fb3-774">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="89fb3-775">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="89fb3-775">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="89fb3-776">Kliknij prawym przyciskiem myszy projekt.</span><span class="sxs-lookup"><span data-stu-id="89fb3-776">Right-click the project.</span></span> <span data-ttu-id="89fb3-777">Wybierz pozycję **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-777">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="89fb3-778">Nazwij folder *Models* .</span><span class="sxs-lookup"><span data-stu-id="89fb3-778">Name the folder *Models*.</span></span>

  ![Nowy folder](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="89fb3-780">Kliknij prawym przyciskiem myszy *Models* folder, a następnie wybierz pozycję **Dodaj** > **nowy plik** > **ogólna** > **pusta Klasa**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-780">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="89fb3-781">Nazwij klasę *TodoItem*, a następnie kliknij pozycję **New (nowy**).</span><span class="sxs-lookup"><span data-stu-id="89fb3-781">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="89fb3-782">Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="89fb3-782">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="89fb3-783">`Id`Właściwość działa jako unikatowy klucz w relacyjnej bazie danych.</span><span class="sxs-lookup"><span data-stu-id="89fb3-783">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="89fb3-784">Klasy modelu mogą przejść do dowolnego miejsca w projekcie, ale *Models* folder jest używany przez Konwencję.</span><span class="sxs-lookup"><span data-stu-id="89fb3-784">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context-21"></a><span data-ttu-id="89fb3-785">Dodawanie kontekstu bazy danych 2,1</span><span class="sxs-lookup"><span data-stu-id="89fb3-785">Add a database context 2.1</span></span>

<span data-ttu-id="89fb3-786">*Kontekst bazy danych* jest główną klasą, która koordynuje Entity Framework funkcji dla modelu danych.</span><span class="sxs-lookup"><span data-stu-id="89fb3-786">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="89fb3-787">Ta klasa jest tworzona przez wyprowadzanie z `Microsoft.EntityFrameworkCore.DbContext` klasy.</span><span class="sxs-lookup"><span data-stu-id="89fb3-787">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="89fb3-788">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="89fb3-788">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="89fb3-789">Kliknij prawym przyciskiem myszy *Models* folder i wybierz polecenie **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-789">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="89fb3-790">Nadaj klasie nazwę *TodoContext* i kliknij przycisk **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-790">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="89fb3-791">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="89fb3-791">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="89fb3-792">Dodaj `TodoContext` klasę do *Models* folderu.</span><span class="sxs-lookup"><span data-stu-id="89fb3-792">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="89fb3-793">Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="89fb3-793">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context-21"></a><span data-ttu-id="89fb3-794">Rejestrowanie kontekstu bazy danych 2,1</span><span class="sxs-lookup"><span data-stu-id="89fb3-794">Register the database context 2.1</span></span>

<span data-ttu-id="89fb3-795">W ASP.NET Core usługi, takie jak kontekst bazy danych, muszą być zarejestrowane z kontenerem [iniekcji zależności (di)](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="89fb3-795">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="89fb3-796">Kontener udostępnia usługę kontrolerom.</span><span class="sxs-lookup"><span data-stu-id="89fb3-796">The container provides the service to controllers.</span></span>

<span data-ttu-id="89fb3-797">Zaktualizuj *Startup.cs* o następujący wyróżniony kod:</span><span class="sxs-lookup"><span data-stu-id="89fb3-797">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="89fb3-798">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="89fb3-798">The preceding code:</span></span>

* <span data-ttu-id="89fb3-799">Usuwa nieużywane `using` deklaracje.</span><span class="sxs-lookup"><span data-stu-id="89fb3-799">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="89fb3-800">Dodaje kontekst bazy danych do kontenera DI.</span><span class="sxs-lookup"><span data-stu-id="89fb3-800">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="89fb3-801">Określa, że kontekst bazy danych będzie używać bazy danych w pamięci.</span><span class="sxs-lookup"><span data-stu-id="89fb3-801">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller-21"></a><span data-ttu-id="89fb3-802">Dodawanie kontrolera 2,1</span><span class="sxs-lookup"><span data-stu-id="89fb3-802">Add a controller 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="89fb3-803">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="89fb3-803">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="89fb3-804">Kliknij prawym przyciskiem myszy folder *controllers* .</span><span class="sxs-lookup"><span data-stu-id="89fb3-804">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="89fb3-805">Wybierz pozycję **Dodaj** > **nowy element**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-805">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="89fb3-806">W oknie dialogowym **Dodaj nowy element** wybierz szablon **Klasa kontrolera interfejsu API** .</span><span class="sxs-lookup"><span data-stu-id="89fb3-806">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="89fb3-807">Nadaj klasie nazwę *TodoController*i wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-807">Name the class *TodoController*, and select **Add**.</span></span>

  ![Okno dialogowe Dodawanie nowego elementu z kontrolerem w polu wyszukiwania i wybranym kontrolerem interfejsu API sieci Web](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="89fb3-809">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="89fb3-809">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="89fb3-810">W folderze *controllers* Utwórz klasę o nazwie `TodoController` .</span><span class="sxs-lookup"><span data-stu-id="89fb3-810">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="89fb3-811">Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="89fb3-811">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="89fb3-812">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="89fb3-812">The preceding code:</span></span>

* <span data-ttu-id="89fb3-813">Definiuje klasę kontrolera interfejsu API bez metod.</span><span class="sxs-lookup"><span data-stu-id="89fb3-813">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="89fb3-814">Oznacza klasę [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atrybutem.</span><span class="sxs-lookup"><span data-stu-id="89fb3-814">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="89fb3-815">Ten atrybut wskazuje, że kontroler odpowiada na żądania interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="89fb3-815">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="89fb3-816">Aby uzyskać informacje o określonych zachowaniach, które włącza atrybut, zobacz <xref:web-api/index> .</span><span class="sxs-lookup"><span data-stu-id="89fb3-816">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="89fb3-817">Używa funkcji DI do iniekcji kontekstu bazy danych ( `TodoContext` ) do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="89fb3-817">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="89fb3-818">Kontekst bazy danych jest używany w każdej z metod [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) w kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="89fb3-818">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="89fb3-819">Dodaje element o nazwie `Item1` do bazy danych, jeśli baza danych jest pusta.</span><span class="sxs-lookup"><span data-stu-id="89fb3-819">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="89fb3-820">Ten kod znajduje się w konstruktorze, więc jest uruchamiany za każdym razem, gdy istnieje nowe żądanie HTTP.</span><span class="sxs-lookup"><span data-stu-id="89fb3-820">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="89fb3-821">Jeśli usuniesz wszystkie elementy, Konstruktor zostanie ponownie utworzony `Item1` przy następnym wywołaniu metody interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="89fb3-821">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="89fb3-822">Dlatego może wyglądać podobnie jak usunięcie nie zadziałało, gdy rzeczywiście zadziałało.</span><span class="sxs-lookup"><span data-stu-id="89fb3-822">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods-21"></a><span data-ttu-id="89fb3-823">Dodaj metody get 2,1</span><span class="sxs-lookup"><span data-stu-id="89fb3-823">Add Get methods 2.1</span></span>

<span data-ttu-id="89fb3-824">Aby udostępnić interfejs API, który pobiera elementy do wykonania, Dodaj następujące metody do `TodoController` klasy:</span><span class="sxs-lookup"><span data-stu-id="89fb3-824">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="89fb3-825">Te metody implementują dwa punkty końcowe GET:</span><span class="sxs-lookup"><span data-stu-id="89fb3-825">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="89fb3-826">Zatrzymaj aplikację, jeśli jest nadal uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="89fb3-826">Stop the app if it's still running.</span></span> <span data-ttu-id="89fb3-827">Następnie uruchom ją ponownie, aby uwzględnić najnowsze zmiany.</span><span class="sxs-lookup"><span data-stu-id="89fb3-827">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="89fb3-828">Przetestuj aplikację, wywołując dwa punkty końcowe z przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="89fb3-828">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="89fb3-829">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="89fb3-829">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="89fb3-830">Następująca odpowiedź HTTP jest generowana przez wywołanie `GetTodoItems` :</span><span class="sxs-lookup"><span data-stu-id="89fb3-830">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths-21"></a><span data-ttu-id="89fb3-831">Ścieżki routingu i adresu URL 2,1</span><span class="sxs-lookup"><span data-stu-id="89fb3-831">Routing and URL paths 2.1</span></span>

<span data-ttu-id="89fb3-832">Ten [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) atrybut oznacza metodę, która reaguje na żądanie HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="89fb3-832">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="89fb3-833">Ścieżka adresu URL dla każdej metody jest zbudowana w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="89fb3-833">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="89fb3-834">Zacznij od ciągu szablonu w `Route` atrybucie kontrolera:</span><span class="sxs-lookup"><span data-stu-id="89fb3-834">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="89fb3-835">Zastąp `[controller]` nazwą kontrolera, którą Konwencją jest nazwa klasy kontrolera minus sufiks "Controller".</span><span class="sxs-lookup"><span data-stu-id="89fb3-835">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="89fb3-836">W przypadku tego przykładu nazwa klasy kontrolera to kontroler do **zrobienia**, więc nazwa kontrolera to "do zrobienia".</span><span class="sxs-lookup"><span data-stu-id="89fb3-836">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="89fb3-837">W ASP.NET Core [routingu](xref:mvc/controllers/routing) jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="89fb3-837">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="89fb3-838">Jeśli `[HttpGet]` atrybut ma szablon trasy (na przykład `[HttpGet("products")]` ), Dodaj go do ścieżki.</span><span class="sxs-lookup"><span data-stu-id="89fb3-838">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="89fb3-839">Ten przykład nie używa szablonu.</span><span class="sxs-lookup"><span data-stu-id="89fb3-839">This sample doesn't use a template.</span></span> <span data-ttu-id="89fb3-840">Aby uzyskać więcej informacji, zobacz temat [Routing atrybutów z atrybutami http [Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="89fb3-840">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="89fb3-841">W poniższej `GetTodoItem` metodzie `"{id}"` jest zmienną zastępczą dla unikatowego identyfikatora elementu do wykonania.</span><span class="sxs-lookup"><span data-stu-id="89fb3-841">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="89fb3-842">Gdy `GetTodoItem` jest wywoływana, wartość `"{id}"` w adresie URL jest podawana do metody w jej `id` parametrze.</span><span class="sxs-lookup"><span data-stu-id="89fb3-842">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values-21"></a><span data-ttu-id="89fb3-843">Wartości zwracane 2,1</span><span class="sxs-lookup"><span data-stu-id="89fb3-843">Return values 2.1</span></span>

<span data-ttu-id="89fb3-844">Zwracany typ `GetTodoItems` `GetTodoItem` metod i jest [ \<T> typem ActionResult](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="89fb3-844">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="89fb3-845">ASP.NET Core automatycznie serializować obiektu do [formatu JSON](https://www.json.org/) i zapisuje kod JSON w treści komunikatu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="89fb3-845">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="89fb3-846">Kod odpowiedzi dla tego typu zwracanego to 200, przy założeniu, że nie istnieją Nieobsłużone wyjątki.</span><span class="sxs-lookup"><span data-stu-id="89fb3-846">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="89fb3-847">Nieobsłużone wyjątki są tłumaczone na błędy 5xx.</span><span class="sxs-lookup"><span data-stu-id="89fb3-847">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="89fb3-848">`ActionResult` typy zwracane mogą reprezentować szeroką gamę kodów stanu HTTP.</span><span class="sxs-lookup"><span data-stu-id="89fb3-848">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="89fb3-849">Na przykład `GetTodoItem` może zwracać dwie różne wartości stanu:</span><span class="sxs-lookup"><span data-stu-id="89fb3-849">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="89fb3-850">Jeśli żaden element nie jest zgodny z żądanym IDENTYFIKATORem, metoda zwróci <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> Kod błędu 404.</span><span class="sxs-lookup"><span data-stu-id="89fb3-850">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="89fb3-851">W przeciwnym razie metoda zwraca 200 z treścią odpowiedzi JSON.</span><span class="sxs-lookup"><span data-stu-id="89fb3-851">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="89fb3-852">Zwracanie `item` wyników w odpowiedzi HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="89fb3-852">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method-21"></a><span data-ttu-id="89fb3-853">Testowanie metody GetTodoItems 2,1</span><span class="sxs-lookup"><span data-stu-id="89fb3-853">Test the GetTodoItems method 2.1</span></span>

<span data-ttu-id="89fb3-854">Ten samouczek używa programu do testowania interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="89fb3-854">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="89fb3-855">Zainstaluj program [Poster](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="89fb3-855">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="89fb3-856">Uruchom aplikację internetową.</span><span class="sxs-lookup"><span data-stu-id="89fb3-856">Start the web app.</span></span>
* <span data-ttu-id="89fb3-857">Uruchom wpis.</span><span class="sxs-lookup"><span data-stu-id="89fb3-857">Start Postman.</span></span>
* <span data-ttu-id="89fb3-858">Wyłącz **weryfikację certyfikatu SSL**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-858">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="89fb3-859">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="89fb3-859">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="89fb3-860">W **File** obszarze > **Ustawienia** pliku (karta**Ogólne** ) Wyłącz **weryfikację certyfikatu SSL**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-860">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="89fb3-861">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="89fb3-861">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="89fb3-862">Z poziomu preferencji programu **Poster**  >  **Preferences** (karta**Ogólne** ) Wyłącz **weryfikację certyfikatu SSL**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-862">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="89fb3-863">Alternatywnie wybierz klucz i wybierz pozycję **Ustawienia**, a następnie wyłącz weryfikację certyfikatu SSL.</span><span class="sxs-lookup"><span data-stu-id="89fb3-863">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="89fb3-864">Po przetestowaniu kontrolera ponownie Włącz weryfikację certyfikatu SSL.</span><span class="sxs-lookup"><span data-stu-id="89fb3-864">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="89fb3-865">Utwórz nowe żądanie.</span><span class="sxs-lookup"><span data-stu-id="89fb3-865">Create a new request.</span></span>
  * <span data-ttu-id="89fb3-866">Ustaw metodę HTTP, aby **uzyskać**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-866">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="89fb3-867">Ustaw identyfikator URI żądania na `https://localhost:<port>/api/todo` .</span><span class="sxs-lookup"><span data-stu-id="89fb3-867">Set the request URI to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="89fb3-868">Na przykład `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="89fb3-868">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="89fb3-869">Ustaw **dwa widoki okienka** w programie Poster.</span><span class="sxs-lookup"><span data-stu-id="89fb3-869">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="89fb3-870">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-870">Select **Send**.</span></span>

![Ogłoś przy użyciu żądania GET](first-web-api/_static/2pv.png)

## <a name="add-a-create-method-21"></a><span data-ttu-id="89fb3-872">Dodawanie metody Create 2,1</span><span class="sxs-lookup"><span data-stu-id="89fb3-872">Add a Create method 2.1</span></span>

<span data-ttu-id="89fb3-873">Dodaj następującą `PostTodoItem` metodę w obszarze *controllers/TodoController. cs*:</span><span class="sxs-lookup"><span data-stu-id="89fb3-873">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="89fb3-874">Poprzedni kod jest metodą POST protokołu HTTP, jak wskazano w [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) atrybucie.</span><span class="sxs-lookup"><span data-stu-id="89fb3-874">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="89fb3-875">Metoda pobiera wartość elementu do wykonania z treści żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="89fb3-875">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="89fb3-876">`CreatedAtAction`Metoda:</span><span class="sxs-lookup"><span data-stu-id="89fb3-876">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="89fb3-877">Zwraca kod stanu HTTP 201, jeśli powodzenie.</span><span class="sxs-lookup"><span data-stu-id="89fb3-877">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="89fb3-878">HTTP 201 to standardowa odpowiedź dla metody POST protokołu HTTP, która tworzy nowy zasób na serwerze.</span><span class="sxs-lookup"><span data-stu-id="89fb3-878">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="89fb3-879">Dodaje `Location` nagłówek do odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="89fb3-879">Adds a `Location` header to the response.</span></span> <span data-ttu-id="89fb3-880">`Location`Nagłówek określa identyfikator URI nowo utworzonego elementu do wykonania.</span><span class="sxs-lookup"><span data-stu-id="89fb3-880">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="89fb3-881">Aby uzyskać więcej informacji, zobacz [10.2.2 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="89fb3-881">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="89fb3-882">Odwołuje się do `GetTodoItem` akcji tworzenia `Location` identyfikatora URI nagłówka.</span><span class="sxs-lookup"><span data-stu-id="89fb3-882">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="89fb3-883">`nameof`Słowo kluczowe języka C# służy do zapobiegania twardemu kodowaniu nazwy akcji w `CreatedAtAction` wywołaniu.</span><span class="sxs-lookup"><span data-stu-id="89fb3-883">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method-21"></a><span data-ttu-id="89fb3-884">Testowanie metody PostTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="89fb3-884">Test the PostTodoItem method 2.1</span></span>

* <span data-ttu-id="89fb3-885">Skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="89fb3-885">Build the project.</span></span>
* <span data-ttu-id="89fb3-886">W programie Poster ustaw metodę HTTP na `POST` .</span><span class="sxs-lookup"><span data-stu-id="89fb3-886">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="89fb3-887">Ustaw identyfikator URI na `https://localhost:<port>/api/TodoItem` .</span><span class="sxs-lookup"><span data-stu-id="89fb3-887">Set the URI to `https://localhost:<port>/api/TodoItem`.</span></span> <span data-ttu-id="89fb3-888">Na przykład `https://localhost:5001/api/TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="89fb3-888">For example, `https://localhost:5001/api/TodoItem`.</span></span>
* <span data-ttu-id="89fb3-889">Wybierz kartę **Treść**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-889">Select the **Body** tab.</span></span>
* <span data-ttu-id="89fb3-890">Wybierz przycisk radiowy **RAW** .</span><span class="sxs-lookup"><span data-stu-id="89fb3-890">Select the **raw** radio button.</span></span>
* <span data-ttu-id="89fb3-891">Ustaw typ na **JSON (Application/JSON)**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-891">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="89fb3-892">W treści żądania wprowadź kod JSON dla elementu do wykonania:</span><span class="sxs-lookup"><span data-stu-id="89fb3-892">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="89fb3-893">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-893">Select **Send**.</span></span>

  ![Ogłoś przy użyciu żądania Create](first-web-api/_static/create.png)

  <span data-ttu-id="89fb3-895">Jeśli wystąpi błąd 405 metody niedozwolonej, jest to prawdopodobnie wynik niekompilowania projektu po dodaniu `PostTodoItem` metody.</span><span class="sxs-lookup"><span data-stu-id="89fb3-895">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri-21"></a><span data-ttu-id="89fb3-896">Testowanie identyfikatora URI nagłówka lokalizacji 2,1</span><span class="sxs-lookup"><span data-stu-id="89fb3-896">Test the location header URI 2.1</span></span>

* <span data-ttu-id="89fb3-897">Wybierz kartę **nagłówki** w okienku **odpowiedź** .</span><span class="sxs-lookup"><span data-stu-id="89fb3-897">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="89fb3-898">Skopiuj wartość nagłówka **lokalizacji** :</span><span class="sxs-lookup"><span data-stu-id="89fb3-898">Copy the **Location** header value:</span></span>

  ![Karta nagłówki w konsoli programu Poster](first-web-api/_static/pmc2.png)

* <span data-ttu-id="89fb3-900">Ustaw metodę, aby uzyskać.</span><span class="sxs-lookup"><span data-stu-id="89fb3-900">Set the method to GET.</span></span>
<span data-ttu-id="89fb3-901">\* Ustaw identyfikator URI na  `https://localhost:<port>/api/TodoItems/2` .</span><span class="sxs-lookup"><span data-stu-id="89fb3-901">\* Set the URI to `https://localhost:<port>/api/TodoItems/2`.</span></span><span data-ttu-id="89fb3-902">Na przykład  `https://localhost:5001/api/TodoItems/2` .</span><span class="sxs-lookup"><span data-stu-id="89fb3-902"> For example, `https://localhost:5001/api/TodoItems/2`.</span></span>
* <span data-ttu-id="89fb3-903">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-903">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method-21"></a><span data-ttu-id="89fb3-904">Dodawanie metody PutTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="89fb3-904">Add a PutTodoItem method 2.1</span></span>

<span data-ttu-id="89fb3-905">Dodaj następującą `PutTodoItem` metodę:</span><span class="sxs-lookup"><span data-stu-id="89fb3-905">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="89fb3-906">`PutTodoItem` jest podobny do `PostTodoItem` , z tą różnicą, że używa protokołu HTTP Put.</span><span class="sxs-lookup"><span data-stu-id="89fb3-906">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="89fb3-907">Odpowiedź to [204 (brak zawartości)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="89fb3-907">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="89fb3-908">Zgodnie ze specyfikacją protokołu HTTP żądanie PUT wymaga, aby klient wysłał całą zaktualizowaną jednostkę, a nie tylko te zmiany.</span><span class="sxs-lookup"><span data-stu-id="89fb3-908">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="89fb3-909">Aby zapewnić obsługę częściowych aktualizacji, użyj [poprawki http](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="89fb3-909">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="89fb3-910">Jeśli wystąpi błąd podczas wywoływania `PutTodoItem` , wywołaj, `GET` Aby upewnić się, że w bazie danych znajduje się element.</span><span class="sxs-lookup"><span data-stu-id="89fb3-910">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method-21"></a><span data-ttu-id="89fb3-911">Testowanie metody PutTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="89fb3-911">Test the PutTodoItem method 2.1</span></span>

<span data-ttu-id="89fb3-912">Ten przykład korzysta z bazy danych w pamięci, która musi zostać zainicjowana za każdym razem, gdy aplikacja zostanie uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="89fb3-912">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="89fb3-913">Przed wykonaniem wywołania PUT musi istnieć element w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="89fb3-913">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="89fb3-914">Przed wykonaniem wywołania PUT należy wywołać metodę GET, aby upewnić się, że istnieje element w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="89fb3-914">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="89fb3-915">Zaktualizuj element do wykonania o identyfikatorze 1 i ustaw jego nazwę na "Źródło danych":</span><span class="sxs-lookup"><span data-stu-id="89fb3-915">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="89fb3-916">Na poniższej ilustracji przedstawiono aktualizację programu Poster:</span><span class="sxs-lookup"><span data-stu-id="89fb3-916">The following image shows the Postman update:</span></span>

![Konsola programu Poster pokazująca odpowiedź 204 (brak zawartości)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method-21"></a><span data-ttu-id="89fb3-918">Dodawanie metody DeleteTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="89fb3-918">Add a DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="89fb3-919">Dodaj następującą `DeleteTodoItem` metodę:</span><span class="sxs-lookup"><span data-stu-id="89fb3-919">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="89fb3-920">`DeleteTodoItem`Odpowiedź to [204 (brak zawartości)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="89fb3-920">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method-21"></a><span data-ttu-id="89fb3-921">Testowanie metody DeleteTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="89fb3-921">Test the DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="89fb3-922">Użyj programu Poster, aby usunąć element do wykonania:</span><span class="sxs-lookup"><span data-stu-id="89fb3-922">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="89fb3-923">Ustaw metodę na `DELETE` .</span><span class="sxs-lookup"><span data-stu-id="89fb3-923">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="89fb3-924">Ustaw identyfikator URI obiektu do usunięcia (na przykład `https://localhost:5001/api/todo/1` ).</span><span class="sxs-lookup"><span data-stu-id="89fb3-924">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="89fb3-925">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="89fb3-925">Select **Send**.</span></span>

<span data-ttu-id="89fb3-926">Przykładowa aplikacja umożliwia usunięcie wszystkich elementów.</span><span class="sxs-lookup"><span data-stu-id="89fb3-926">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="89fb3-927">Jednak po usunięciu ostatniego elementu jest on tworzony przez konstruktora klasy modelu przy następnym wywołaniu interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="89fb3-927">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript-21"></a><span data-ttu-id="89fb3-928">Wywoływanie interfejsu API sieci Web przy użyciu języka JavaScript 2,1</span><span class="sxs-lookup"><span data-stu-id="89fb3-928">Call the web API with JavaScript 2.1</span></span>

<span data-ttu-id="89fb3-929">W tej sekcji zostanie dodana strona HTML, która używa języka JavaScript do wywoływania internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="89fb3-929">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="89fb3-930">jQuery inicjuje żądanie.</span><span class="sxs-lookup"><span data-stu-id="89fb3-930">jQuery initiates the request.</span></span> <span data-ttu-id="89fb3-931">Język JavaScript aktualizuje stronę ze szczegółowymi informacjami z odpowiedzi internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="89fb3-931">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="89fb3-932">Skonfiguruj aplikację do [obsługi plików statycznych](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) i [Włącz domyślne mapowanie plików](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) , aktualizując *Startup.cs* z następującym wyróżnionym kodem:</span><span class="sxs-lookup"><span data-stu-id="89fb3-932">Configure the app to [serve static files](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) and [enable default file mapping](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="89fb3-933">Utwórz folder *wwwroot* w katalogu projektu.</span><span class="sxs-lookup"><span data-stu-id="89fb3-933">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="89fb3-934">Dodaj plik HTML o nazwie *index.html* do katalogu *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="89fb3-934">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="89fb3-935">Zastąp jego zawartość następującym znacznikiem:</span><span class="sxs-lookup"><span data-stu-id="89fb3-935">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="89fb3-936">Dodaj plik języka JavaScript o nazwie *site.js* do katalogu *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="89fb3-936">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="89fb3-937">Zastąp zawartość poniższym kodem:</span><span class="sxs-lookup"><span data-stu-id="89fb3-937">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="89fb3-938">Zmiana ustawień uruchamiania projektu ASP.NET Core może być wymagana do lokalnego przetestowania strony HTML:</span><span class="sxs-lookup"><span data-stu-id="89fb3-938">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="89fb3-939">Otwórz *Properties\launchSettings.jsna*.</span><span class="sxs-lookup"><span data-stu-id="89fb3-939">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="89fb3-940">Usuń `launchUrl` Właściwość, aby wymusić otwieranie przez aplikację w *index.html* &mdash; domyślnego pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="89fb3-940">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="89fb3-941">Ten przykład wywołuje wszystkie metody CRUD internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="89fb3-941">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="89fb3-942">Poniżej znajdują się wyjaśnienia wywołań interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="89fb3-942">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items-21"></a><span data-ttu-id="89fb3-943">Pobierz listę elementów do wykonania 2,1</span><span class="sxs-lookup"><span data-stu-id="89fb3-943">Get a list of to-do items 2.1</span></span>

<span data-ttu-id="89fb3-944">jQuery wysyła żądanie HTTP GET do internetowego interfejsu API, który zwraca kod JSON reprezentujący tablicę elementów do wykonania.</span><span class="sxs-lookup"><span data-stu-id="89fb3-944">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="89fb3-945">`success`Funkcja wywołania zwrotnego jest wywoływana, jeśli żądanie zakończy się pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="89fb3-945">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="89fb3-946">W wywołaniu zwrotnym model DOM jest aktualizowany przy użyciu informacji o tym do wykonania.</span><span class="sxs-lookup"><span data-stu-id="89fb3-946">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item-21"></a><span data-ttu-id="89fb3-947">Dodaj element do wykonania 2,1</span><span class="sxs-lookup"><span data-stu-id="89fb3-947">Add a to-do item 2.1</span></span>

<span data-ttu-id="89fb3-948">jQuery wysyła żądanie HTTP POST z elementem do wykonania w treści żądania.</span><span class="sxs-lookup"><span data-stu-id="89fb3-948">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="89fb3-949">`accepts`Opcje i `contentType` są ustawione na, aby `application/json` określić typ nośnika odbierany i wysyłany.</span><span class="sxs-lookup"><span data-stu-id="89fb3-949">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="89fb3-950">Element do wykonania jest konwertowany na format JSON przy użyciu [formatu JSON. stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="89fb3-950">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="89fb3-951">Gdy interfejs API zwraca kod stanu pomyślnego, `getData` Funkcja jest wywoływana w celu zaktualizowania tabeli HTML.</span><span class="sxs-lookup"><span data-stu-id="89fb3-951">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item-21"></a><span data-ttu-id="89fb3-952">Aktualizowanie elementu do wykonania 2,1</span><span class="sxs-lookup"><span data-stu-id="89fb3-952">Update a to-do item 2.1</span></span>

<span data-ttu-id="89fb3-953">Aktualizowanie elementu do wykonania jest podobne do dodawania jednego.</span><span class="sxs-lookup"><span data-stu-id="89fb3-953">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="89fb3-954">`url`Zmiany mające na celu dodanie unikatowego identyfikatora elementu i `type` is `PUT` .</span><span class="sxs-lookup"><span data-stu-id="89fb3-954">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item-21"></a><span data-ttu-id="89fb3-955">Usuń element do wykonania 2,1</span><span class="sxs-lookup"><span data-stu-id="89fb3-955">Delete a to-do item 2.1</span></span>

<span data-ttu-id="89fb3-956">Usuwanie elementu do wykonania jest realizowane przez ustawienie `type` w WYWOŁANIU AJAX `DELETE` i określenie unikatowego identyfikatora elementu w adresie URL.</span><span class="sxs-lookup"><span data-stu-id="89fb3-956">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api-21"></a><span data-ttu-id="89fb3-957">Dodawanie obsługi uwierzytelniania do internetowego interfejsu API 2,1</span><span class="sxs-lookup"><span data-stu-id="89fb3-957">Add authentication support to a web API 2.1</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources-21"></a><span data-ttu-id="89fb3-958">Dodatkowe zasoby 2,1</span><span class="sxs-lookup"><span data-stu-id="89fb3-958">Additional resources 2.1</span></span>

<span data-ttu-id="89fb3-959">[Wyświetl lub Pobierz przykładowy kod dla tego samouczka](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="89fb3-959">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="89fb3-960">Zobacz artykuł [jak pobrać](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="89fb3-960">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="89fb3-961">Więcej informacji można znaleźć w następujących zasobach:</span><span class="sxs-lookup"><span data-stu-id="89fb3-961">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="89fb3-962">Wersja tego samouczka usługi YouTube</span><span class="sxs-lookup"><span data-stu-id="89fb3-962">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
