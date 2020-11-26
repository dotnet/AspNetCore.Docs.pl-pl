---
title: 'Samouczek: Tworzenie internetowego interfejsu API za pomocą ASP.NET Core'
author: rick-anderson
description: Dowiedz się, jak utworzyć internetowy interfejs API za pomocą ASP.NET Core.
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 08/13/2020
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
- Models
uid: tutorials/first-web-api
ms.openlocfilehash: ccbfc27eb89e23938a69f0ab4cb306d6a4136889
ms.sourcegitcommit: fe2e3174c34bee1e425c6e52dd8f663fe52b8756
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96175055"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="05098-103">Samouczek: Tworzenie internetowego interfejsu API za pomocą ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="05098-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="05098-104">Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirka Larkin](https://twitter.com/serpent5)i [Jan Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="05098-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="05098-105">Ten samouczek uczy się podstaw tworzenia interfejsu API sieci Web za pomocą ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="05098-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="05098-106">Z tego samouczka dowiesz się, jak wykonywać następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="05098-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="05098-107">Utwórz projekt interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="05098-107">Create a web API project.</span></span>
> * <span data-ttu-id="05098-108">Dodaj klasę modelu i kontekst bazy danych.</span><span class="sxs-lookup"><span data-stu-id="05098-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="05098-109">Tworzy szkielet kontrolera z metodami CRUD.</span><span class="sxs-lookup"><span data-stu-id="05098-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="05098-110">Skonfiguruj Routing, ścieżki URL i wartości zwracane.</span><span class="sxs-lookup"><span data-stu-id="05098-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="05098-111">Wywołaj interfejs API sieci Web za pomocą programu Poster.</span><span class="sxs-lookup"><span data-stu-id="05098-111">Call the web API with Postman.</span></span>

<span data-ttu-id="05098-112">Na końcu znajduje się internetowy interfejs API, który może zarządzać elementami do wykonania przechowywanymi w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="05098-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="05098-113">Omówienie</span><span class="sxs-lookup"><span data-stu-id="05098-113">Overview</span></span>

<span data-ttu-id="05098-114">Ten samouczek tworzy następujący interfejs API:</span><span class="sxs-lookup"><span data-stu-id="05098-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="05098-115">Interfejs API</span><span class="sxs-lookup"><span data-stu-id="05098-115">API</span></span> | <span data-ttu-id="05098-116">Opis</span><span class="sxs-lookup"><span data-stu-id="05098-116">Description</span></span> | <span data-ttu-id="05098-117">Treść żądania</span><span class="sxs-lookup"><span data-stu-id="05098-117">Request body</span></span> | <span data-ttu-id="05098-118">Treść odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="05098-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="05098-119">Pobierz wszystkie elementy do wykonania</span><span class="sxs-lookup"><span data-stu-id="05098-119">Get all to-do items</span></span> | <span data-ttu-id="05098-120">Brak</span><span class="sxs-lookup"><span data-stu-id="05098-120">None</span></span> | <span data-ttu-id="05098-121">Tablica elementów do wykonania</span><span class="sxs-lookup"><span data-stu-id="05098-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="05098-122">Pobieranie elementu według identyfikatora</span><span class="sxs-lookup"><span data-stu-id="05098-122">Get an item by ID</span></span> | <span data-ttu-id="05098-123">Brak</span><span class="sxs-lookup"><span data-stu-id="05098-123">None</span></span> | <span data-ttu-id="05098-124">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="05098-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="05098-125">Dodaj nowy element</span><span class="sxs-lookup"><span data-stu-id="05098-125">Add a new item</span></span> | <span data-ttu-id="05098-126">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="05098-126">To-do item</span></span> | <span data-ttu-id="05098-127">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="05098-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="05098-128">Aktualizowanie istniejącego elementu &nbsp;</span><span class="sxs-lookup"><span data-stu-id="05098-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="05098-129">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="05098-129">To-do item</span></span> | <span data-ttu-id="05098-130">Brak</span><span class="sxs-lookup"><span data-stu-id="05098-130">None</span></span> |
|<span data-ttu-id="05098-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="05098-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="05098-132">Usuń element &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="05098-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="05098-133">Brak</span><span class="sxs-lookup"><span data-stu-id="05098-133">None</span></span> | <span data-ttu-id="05098-134">Brak</span><span class="sxs-lookup"><span data-stu-id="05098-134">None</span></span>|

<span data-ttu-id="05098-135">Na poniższym diagramie przedstawiono projekt aplikacji.</span><span class="sxs-lookup"><span data-stu-id="05098-135">The following diagram shows the design of the app.</span></span>

![Klient jest reprezentowany przez pole po lewej stronie.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="05098-141">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="05098-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="05098-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="05098-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="05098-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="05098-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="05098-144">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="05098-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="05098-145">Tworzenie projektu sieci Web</span><span class="sxs-lookup"><span data-stu-id="05098-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="05098-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="05098-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="05098-147">Z menu **plik** wybierz pozycję **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="05098-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="05098-148">Wybierz szablon **aplikacja sieci Web ASP.NET Core** a następnie kliknij przycisk **dalej**.</span><span class="sxs-lookup"><span data-stu-id="05098-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="05098-149">Nazwij projekt *TodoApi* i kliknij pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="05098-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="05098-150">W oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** upewnij się, że wybrano opcję **.net Core** i **ASP.NET Core 5,0** .</span><span class="sxs-lookup"><span data-stu-id="05098-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 5.0** are selected.</span></span> <span data-ttu-id="05098-151">Wybierz szablon **interfejsu API** i kliknij przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="05098-151">Select the **API** template and click **Create**.</span></span>

![Okno dialogowe programu VS New Project](first-web-api/_static/5/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="05098-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="05098-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="05098-154">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="05098-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="05098-155">Zmień katalog ( `cd` ) do folderu, który będzie zawierać folder projektu.</span><span class="sxs-lookup"><span data-stu-id="05098-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="05098-156">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="05098-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="05098-157">Gdy zostanie wyświetlone okno dialogowe z pytaniem, czy chcesz dodać wymagane zasoby do projektu, wybierz opcję **tak**.</span><span class="sxs-lookup"><span data-stu-id="05098-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="05098-158">Poprzednie polecenia:</span><span class="sxs-lookup"><span data-stu-id="05098-158">The preceding commands:</span></span>

  * <span data-ttu-id="05098-159">Tworzy nowy projekt internetowego interfejsu API i otwiera go w Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="05098-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="05098-160">Dodaje pakiety NuGet, które są wymagane w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="05098-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="05098-161">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="05098-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="05098-162">Wybierz pozycję **plik** > **nowe rozwiązanie**.</span><span class="sxs-lookup"><span data-stu-id="05098-162">Select **File** > **New Solution**.</span></span>

  ![macOS nowe rozwiązanie](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="05098-164">W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz **.NET Core** pozycję  >  **App**  >  **interfejs API** aplikacji .NET Core  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="05098-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="05098-165">W wersji 8,6 lub nowszej wybierz pozycję **Web and Console**  >  **App**  >  **interfejs API**  >  **Next** aplikacji sieci Web i konsoli.</span><span class="sxs-lookup"><span data-stu-id="05098-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![Wybór szablonu interfejsu API macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="05098-167">W oknie dialogowym **Konfigurowanie nowego interfejsu API sieci Web ASP.NET Core** wybierz najnowszą platformę **docelową**.NET Core 5. x.</span><span class="sxs-lookup"><span data-stu-id="05098-167">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 5.x **Target Framework**.</span></span> <span data-ttu-id="05098-168">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="05098-168">Select **Next**.</span></span>

* <span data-ttu-id="05098-169">Wprowadź *TodoApi* jako **nazwę projektu** , a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="05098-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![okno dialogowe konfiguracji](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="05098-171">Otwórz Terminal poleceń w folderze projektu i uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="05098-171">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-project"></a><span data-ttu-id="05098-172">Testowanie projektu</span><span class="sxs-lookup"><span data-stu-id="05098-172">Test the project</span></span>

<span data-ttu-id="05098-173">Szablon projektu tworzy `WeatherForecast` interfejs API z obsługą [struktury Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span><span class="sxs-lookup"><span data-stu-id="05098-173">The project template creates a `WeatherForecast` API with support for [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="05098-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="05098-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="05098-175">Naciśnij klawisze CTRL + F5, aby uruchomić bez debugera.</span><span class="sxs-lookup"><span data-stu-id="05098-175">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="05098-176">Zostanie uruchomiony program Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="05098-176">Visual Studio launches:</span></span>

* <span data-ttu-id="05098-177">Serwer sieci Web IIS Express.</span><span class="sxs-lookup"><span data-stu-id="05098-177">The IIS Express web server.</span></span>
* <span data-ttu-id="05098-178">Domyślna przeglądarka i nawiguje do `https://localhost:<port>/swagger/index.html` , gdzie `<port>` jest losowo wybranym numerem portu.</span><span class="sxs-lookup"><span data-stu-id="05098-178">The default browser and navigates to `https://localhost:<port>/swagger/index.html`, where `<port>` is a randomly chosen port number.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="05098-179">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="05098-179">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/trustCertVSC.md)]

<span data-ttu-id="05098-180">Naciśnij klawisze CTRL + F5, aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="05098-180">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="05098-181">W przeglądarce przejdź do następującego adresu URL: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span><span class="sxs-lookup"><span data-stu-id="05098-181">In a browser, go to following URL: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="05098-182">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="05098-182">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="05098-183">Wybierz pozycję **Uruchom**  >  **Rozpocznij debugowanie** , aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="05098-183">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="05098-184">Visual Studio dla komputerów Mac uruchamia przeglądarkę i nawiguje do `https://localhost:<port>` , gdzie `<port>` jest losowo wybranym numerem portu.</span><span class="sxs-lookup"><span data-stu-id="05098-184">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="05098-185">Zwracany jest błąd HTTP 404 (nie znaleziono).</span><span class="sxs-lookup"><span data-stu-id="05098-185">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="05098-186">Dołącz `/swagger` do adresu URL (Zmień adres URL na `https://localhost:<port>/swagger` ).</span><span class="sxs-lookup"><span data-stu-id="05098-186">Append `/swagger` to the URL (change the URL to `https://localhost:<port>/swagger`).</span></span>

---

<span data-ttu-id="05098-187">`/swagger/index.html`Zostanie wyświetlona strona Swagger.</span><span class="sxs-lookup"><span data-stu-id="05098-187">The Swagger page `/swagger/index.html` is displayed.</span></span> <span data-ttu-id="05098-188">Wybierz pozycję **Pobierz**  >  **spróbuj**  >  **wykonać** operację.</span><span class="sxs-lookup"><span data-stu-id="05098-188">Select **GET** > **Try it out** > **Execute**.</span></span> <span data-ttu-id="05098-189">Zostanie wyświetlona strona:</span><span class="sxs-lookup"><span data-stu-id="05098-189">The page displays:</span></span>

* <span data-ttu-id="05098-190">[Zwinięcie](https://curl.haxx.se/) polecenie do testowania interfejsu API WeatherForecast.</span><span class="sxs-lookup"><span data-stu-id="05098-190">The [Curl](https://curl.haxx.se/) command to test the WeatherForecast API.</span></span>
* <span data-ttu-id="05098-191">Adres URL służący do testowania interfejsu API WeatherForecast.</span><span class="sxs-lookup"><span data-stu-id="05098-191">The URL to test the WeatherForecast API.</span></span>
* <span data-ttu-id="05098-192">Kod odpowiedzi, treść i nagłówki.</span><span class="sxs-lookup"><span data-stu-id="05098-192">The response code, body, and headers.</span></span>
* <span data-ttu-id="05098-193">Pole listy rozwijanej z typami nośników oraz przykładową wartością i schematem.</span><span class="sxs-lookup"><span data-stu-id="05098-193">A drop down list box with media types and the example value and schema.</span></span>

<!-- Review: Do we care the IE generates several errors. It shows the data, but with  Unrecognized response type; displaying content as text.
-->
<span data-ttu-id="05098-194">Struktura Swagger służy do generowania przydatnej dokumentacji i stron pomocy dla interfejsów API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="05098-194">Swagger is used to generate useful documentation and help pages for web APIs.</span></span> <span data-ttu-id="05098-195">Ten samouczek koncentruje się na tworzeniu interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="05098-195">This tutorial focuses on creating a web API.</span></span> <span data-ttu-id="05098-196">Aby uzyskać więcej informacji na temat struktury Swagger, zobacz <xref:tutorials/web-api-help-pages-using-swagger> .</span><span class="sxs-lookup"><span data-stu-id="05098-196">For more information on Swagger, see <xref:tutorials/web-api-help-pages-using-swagger>.</span></span>

<span data-ttu-id="05098-197">Skopiuj i wklej **adres URL żądania** w przeglądarce:  `https://localhost:<port>/WeatherForecast`</span><span class="sxs-lookup"><span data-stu-id="05098-197">Copy and paste the **Request URL** in the browser:  `https://localhost:<port>/WeatherForecast`</span></span>

<span data-ttu-id="05098-198">Zwracany jest kod JSON podobny do następującego:</span><span class="sxs-lookup"><span data-stu-id="05098-198">JSON similar to the following is returned:</span></span>

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

### <a name="update-the-launchurl"></a><span data-ttu-id="05098-199">Aktualizowanie launchUrl</span><span class="sxs-lookup"><span data-stu-id="05098-199">Update the launchUrl</span></span>

<span data-ttu-id="05098-200">W *Properties\launchSettings.json* Update `launchUrl` od `"swagger"` do `"api/TodoItems"` :</span><span class="sxs-lookup"><span data-stu-id="05098-200">In *Properties\launchSettings.json*, update `launchUrl` from `"swagger"` to `"api/TodoItems"`:</span></span>

```json
"launchUrl": "api/TodoItems",
```

<span data-ttu-id="05098-201">Ze względu na to, że program Swagger został usunięty, poprzedzający znacznik zmienia adres URL, który jest uruchamiany w metodzie GET kontrolera dodanej w poniższych sekcjach.</span><span class="sxs-lookup"><span data-stu-id="05098-201">Because Swagger has been removed, the preceding markup changes the URL that is launched to the GET method of the controller added in the following sections.</span></span>

## <a name="add-a-model-class"></a><span data-ttu-id="05098-202">Dodaj klasę modelu</span><span class="sxs-lookup"><span data-stu-id="05098-202">Add a model class</span></span>

<span data-ttu-id="05098-203">*Model* to zestaw klas, które reprezentują dane zarządzane przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="05098-203">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="05098-204">Model tej aplikacji jest pojedynczą `TodoItem` klasą.</span><span class="sxs-lookup"><span data-stu-id="05098-204">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="05098-205">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="05098-205">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="05098-206">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt.</span><span class="sxs-lookup"><span data-stu-id="05098-206">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="05098-207">Wybierz pozycję **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="05098-207">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="05098-208">Nazwij folder *Models* .</span><span class="sxs-lookup"><span data-stu-id="05098-208">Name the folder *Models*.</span></span>

* <span data-ttu-id="05098-209">Kliknij prawym przyciskiem myszy *Models* folder i wybierz polecenie **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="05098-209">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="05098-210">Nadaj klasie nazwę *TodoItem* i wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="05098-210">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="05098-211">Zastąp kod szablonu następującym:</span><span class="sxs-lookup"><span data-stu-id="05098-211">Replace the template code with the following:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="05098-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="05098-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="05098-213">Dodaj folder o nazwie *Models* .</span><span class="sxs-lookup"><span data-stu-id="05098-213">Add a folder named *Models*.</span></span>

* <span data-ttu-id="05098-214">Dodaj `TodoItem` do *Models* folderu klasę o następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="05098-214">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="05098-215">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="05098-215">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="05098-216">Kliknij prawym przyciskiem myszy projekt.</span><span class="sxs-lookup"><span data-stu-id="05098-216">Right-click the project.</span></span> <span data-ttu-id="05098-217">Wybierz pozycję **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="05098-217">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="05098-218">Nazwij folder *Models* .</span><span class="sxs-lookup"><span data-stu-id="05098-218">Name the folder *Models*.</span></span>

  ![Nowy folder](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="05098-220">Kliknij prawym przyciskiem myszy *Models* folder, a następnie wybierz pozycję **Dodaj** > **nowy plik** > **ogólna** > **pusta Klasa**.</span><span class="sxs-lookup"><span data-stu-id="05098-220">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="05098-221">Nazwij klasę *TodoItem*, a następnie kliknij pozycję **New (nowy**).</span><span class="sxs-lookup"><span data-stu-id="05098-221">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="05098-222">Zastąp kod szablonu następującym:</span><span class="sxs-lookup"><span data-stu-id="05098-222">Replace the template code with the following:</span></span>

---

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="05098-223">`Id`Właściwość działa jako unikatowy klucz w relacyjnej bazie danych.</span><span class="sxs-lookup"><span data-stu-id="05098-223">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="05098-224">Klasy modelu mogą przejść do dowolnego miejsca w projekcie, ale *Models* folder jest używany przez Konwencję.</span><span class="sxs-lookup"><span data-stu-id="05098-224">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="05098-225">Dodawanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="05098-225">Add a database context</span></span>

<span data-ttu-id="05098-226">*Kontekst bazy danych* jest główną klasą, która koordynuje Entity Framework funkcji dla modelu danych.</span><span class="sxs-lookup"><span data-stu-id="05098-226">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="05098-227">Ta klasa jest tworzona przez wyprowadzanie z <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> klasy.</span><span class="sxs-lookup"><span data-stu-id="05098-227">This class is created by deriving from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="05098-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="05098-228">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="05098-229">Dodawanie pakietów NuGet</span><span class="sxs-lookup"><span data-stu-id="05098-229">Add NuGet packages</span></span>

* <span data-ttu-id="05098-230">W menu **Narzędzia** wybierz pozycję **menedżer pakietów NuGet > zarządzanie pakietami NuGet dla rozwiązania**.</span><span class="sxs-lookup"><span data-stu-id="05098-230">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="05098-231">Wybierz kartę **Przeglądaj** , a następnie w polu wyszukiwania wprowadź ciąg **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="05098-231">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Delete this line at RTM -->
* <span data-ttu-id="05098-232">W lewym okienku wybierz pozycję **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="05098-232">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="05098-233">Zaznacz pole wyboru **projekt** w prawym okienku, a następnie wybierz pozycję **Zainstaluj**.</span><span class="sxs-lookup"><span data-stu-id="05098-233">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="05098-234">Użyj powyższych instrukcji, aby dodać pakiet NuGet **Microsoft. EntityFrameworkCore. inMemory** .</span><span class="sxs-lookup"><span data-stu-id="05098-234">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Update this image at RTM -->
<span data-ttu-id="05098-235">![Menedżer pakietów NuGet](first-web-api/_static/5/vsNuGet.png)</span><span class="sxs-lookup"><span data-stu-id="05098-235">![NuGet Package Manager](first-web-api/_static/5/vsNuGet.png)</span></span>

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="05098-236">Dodawanie kontekstu bazy danych TodoContext</span><span class="sxs-lookup"><span data-stu-id="05098-236">Add the TodoContext database context</span></span>

* <span data-ttu-id="05098-237">Kliknij prawym przyciskiem myszy *Models* folder i wybierz polecenie **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="05098-237">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="05098-238">Nadaj klasie nazwę *TodoContext* i kliknij przycisk **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="05098-238">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="05098-239">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="05098-239">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="05098-240">Dodaj `TodoContext` klasę do *Models* folderu.</span><span class="sxs-lookup"><span data-stu-id="05098-240">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="05098-241">Wprowadź następujący kod:</span><span class="sxs-lookup"><span data-stu-id="05098-241">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="05098-242">Rejestrowanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="05098-242">Register the database context</span></span>

<span data-ttu-id="05098-243">W ASP.NET Core usługi, takie jak kontekst bazy danych, muszą być zarejestrowane z kontenerem [iniekcji zależności (di)](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="05098-243">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="05098-244">Kontener udostępnia usługę kontrolerom.</span><span class="sxs-lookup"><span data-stu-id="05098-244">The container provides the service to controllers.</span></span>

<span data-ttu-id="05098-245">Zaktualizuj *Startup.cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="05098-245">Update *Startup.cs* with the following code:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="05098-246">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="05098-246">The preceding code:</span></span>

* <span data-ttu-id="05098-247">Usuwa wywołania struktury Swagger.</span><span class="sxs-lookup"><span data-stu-id="05098-247">Removes the Swagger calls.</span></span>
* <span data-ttu-id="05098-248">Usuwa nieużywane `using` deklaracje.</span><span class="sxs-lookup"><span data-stu-id="05098-248">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="05098-249">Dodaje kontekst bazy danych do kontenera DI.</span><span class="sxs-lookup"><span data-stu-id="05098-249">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="05098-250">Określa, że kontekst bazy danych będzie używać bazy danych w pamięci.</span><span class="sxs-lookup"><span data-stu-id="05098-250">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="05098-251">Tworzenie szkieletu kontrolera</span><span class="sxs-lookup"><span data-stu-id="05098-251">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="05098-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="05098-252">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="05098-253">Kliknij prawym przyciskiem myszy folder *controllers* .</span><span class="sxs-lookup"><span data-stu-id="05098-253">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="05098-254">Wybierz pozycję **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="05098-254">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="05098-255">Wybierz pozycję **kontroler interfejsu API z akcjami, używając Entity Framework**, a następnie wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="05098-255">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="05098-256">Na stronie **Dodawanie kontrolera interfejsu API z akcjami przy użyciu Entity Framework** dialogowego:</span><span class="sxs-lookup"><span data-stu-id="05098-256">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="05098-257">Wybierz pozycję **TodoItem (TodoApi. Models )** w **klasie model**.</span><span class="sxs-lookup"><span data-stu-id="05098-257">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="05098-258">Wybierz pozycję **TodoContext (TodoApi. Models )** w **klasie kontekstu danych**.</span><span class="sxs-lookup"><span data-stu-id="05098-258">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="05098-259">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="05098-259">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="05098-260">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="05098-260">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="05098-261">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="05098-261">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet tool update -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="05098-262">Poprzednie polecenia:</span><span class="sxs-lookup"><span data-stu-id="05098-262">The preceding commands:</span></span>

* <span data-ttu-id="05098-263">Dodaj pakiety NuGet wymagane do tworzenia szkieletów.</span><span class="sxs-lookup"><span data-stu-id="05098-263">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="05098-264">Instaluje aparat tworzenia szkieletu ( `dotnet-aspnet-codegenerator` ).</span><span class="sxs-lookup"><span data-stu-id="05098-264">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="05098-265">Szkielety `TodoItemsController` .</span><span class="sxs-lookup"><span data-stu-id="05098-265">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="05098-266">Wygenerowany kod:</span><span class="sxs-lookup"><span data-stu-id="05098-266">The generated code:</span></span>

* <span data-ttu-id="05098-267">Oznacza klasę [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atrybutem.</span><span class="sxs-lookup"><span data-stu-id="05098-267">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="05098-268">Ten atrybut wskazuje, że kontroler odpowiada na żądania interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="05098-268">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="05098-269">Aby uzyskać informacje o określonych zachowaniach, które włącza atrybut, zobacz <xref:web-api/index> .</span><span class="sxs-lookup"><span data-stu-id="05098-269">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="05098-270">Używa funkcji DI do iniekcji kontekstu bazy danych ( `TodoContext` ) do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="05098-270">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="05098-271">Kontekst bazy danych jest używany w każdej z metod [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) w kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="05098-271">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="05098-272">Szablony ASP.NET Core dla:</span><span class="sxs-lookup"><span data-stu-id="05098-272">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="05098-273">Kontrolery z widokami obejmują `[action]` szablon trasy.</span><span class="sxs-lookup"><span data-stu-id="05098-273">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="05098-274">Kontrolery interfejsu API nie należą `[action]` do szablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="05098-274">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="05098-275">Gdy `[action]` token nie znajduje się w szablonie trasy, nazwa [akcji](xref:mvc/controllers/routing#action) jest wykluczona z trasy.</span><span class="sxs-lookup"><span data-stu-id="05098-275">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="05098-276">Oznacza to, że nazwa metody skojarzonej z akcją nie jest używana w zgodnej trasie.</span><span class="sxs-lookup"><span data-stu-id="05098-276">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="update-the-posttodoitem-create-method"></a><span data-ttu-id="05098-277">Aktualizowanie metody PostTodoItem Create</span><span class="sxs-lookup"><span data-stu-id="05098-277">Update the PostTodoItem create method</span></span>

<span data-ttu-id="05098-278">Zastąp instrukcję return w, `PostTodoItem` Aby użyć operatora [nameof](/dotnet/csharp/language-reference/operators/nameof) :</span><span class="sxs-lookup"><span data-stu-id="05098-278">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="05098-279">Poprzedni kod jest metodą POST protokołu HTTP, jak wskazano w [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) atrybucie.</span><span class="sxs-lookup"><span data-stu-id="05098-279">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="05098-280">Metoda pobiera wartość elementu do wykonania z treści żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="05098-280">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="05098-281">Aby uzyskać więcej informacji, zobacz temat [Routing atrybutów z atrybutami http [Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="05098-281">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="05098-282"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>Metoda:</span><span class="sxs-lookup"><span data-stu-id="05098-282">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="05098-283">W razie powodzenia zwraca [kod stanu HTTP 201](https://developer.mozilla.org/docs/Web/HTTP/Status/201) .</span><span class="sxs-lookup"><span data-stu-id="05098-283">Returns an [HTTP 201 status code](https://developer.mozilla.org/docs/Web/HTTP/Status/201) if successful.</span></span> <span data-ttu-id="05098-284">HTTP 201 to standardowa odpowiedź dla metody POST protokołu HTTP, która tworzy nowy zasób na serwerze.</span><span class="sxs-lookup"><span data-stu-id="05098-284">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="05098-285">Dodaje nagłówek [lokalizacji](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) do odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="05098-285">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="05098-286">`Location`Nagłówek określa [Identyfikator URI](https://developer.mozilla.org/docs/Glossary/URI) nowo utworzonego elementu do wykonania.</span><span class="sxs-lookup"><span data-stu-id="05098-286">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="05098-287">Aby uzyskać więcej informacji, zobacz [10.2.2 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="05098-287">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="05098-288">Odwołuje się do `GetTodoItem` akcji tworzenia `Location` identyfikatora URI nagłówka.</span><span class="sxs-lookup"><span data-stu-id="05098-288">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="05098-289">`nameof`Słowo kluczowe języka C# służy do zapobiegania twardemu kodowaniu nazwy akcji w `CreatedAtAction` wywołaniu.</span><span class="sxs-lookup"><span data-stu-id="05098-289">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="05098-290">Zainstaluj program Poster</span><span class="sxs-lookup"><span data-stu-id="05098-290">Install Postman</span></span>

<span data-ttu-id="05098-291">Ten samouczek używa programu do testowania interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="05098-291">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="05098-292">Zainstaluj program [Poster](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="05098-292">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="05098-293">Uruchom aplikację internetową.</span><span class="sxs-lookup"><span data-stu-id="05098-293">Start the web app.</span></span>
* <span data-ttu-id="05098-294">Uruchom wpis.</span><span class="sxs-lookup"><span data-stu-id="05098-294">Start Postman.</span></span>
* <span data-ttu-id="05098-295">Wyłącz **weryfikację certyfikatu SSL**</span><span class="sxs-lookup"><span data-stu-id="05098-295">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="05098-296">W **File** obszarze > **Ustawienia** pliku (karta **Ogólne** ) Wyłącz **weryfikację certyfikatu SSL**.</span><span class="sxs-lookup"><span data-stu-id="05098-296">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="05098-297">Po przetestowaniu kontrolera ponownie Włącz weryfikację certyfikatu SSL.</span><span class="sxs-lookup"><span data-stu-id="05098-297">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="05098-298">Test PostTodoItem za pomocą programu Poster</span><span class="sxs-lookup"><span data-stu-id="05098-298">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="05098-299">Utwórz nowe żądanie.</span><span class="sxs-lookup"><span data-stu-id="05098-299">Create a new request.</span></span>
* <span data-ttu-id="05098-300">Ustaw metodę HTTP na `POST` .</span><span class="sxs-lookup"><span data-stu-id="05098-300">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="05098-301">Ustaw identyfikator URI na `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="05098-301">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="05098-302">Na przykład `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="05098-302">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="05098-303">Wybierz kartę **Treść**.</span><span class="sxs-lookup"><span data-stu-id="05098-303">Select the **Body** tab.</span></span>
* <span data-ttu-id="05098-304">Wybierz przycisk radiowy **RAW** .</span><span class="sxs-lookup"><span data-stu-id="05098-304">Select the **raw** radio button.</span></span>
* <span data-ttu-id="05098-305">Ustaw typ na **JSON (Application/JSON)**.</span><span class="sxs-lookup"><span data-stu-id="05098-305">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="05098-306">W treści żądania wprowadź kod JSON dla elementu do wykonania:</span><span class="sxs-lookup"><span data-stu-id="05098-306">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="05098-307">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="05098-307">Select **Send**.</span></span>

  ![Ogłoś przy użyciu żądania Create](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="05098-309">Testowanie identyfikatora URI nagłówka lokalizacji</span><span class="sxs-lookup"><span data-stu-id="05098-309">Test the location header URI</span></span>

<span data-ttu-id="05098-310">Identyfikator URI nagłówka lokalizacji może zostać przetestowany w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="05098-310">The location header URI can be tested in the browser.</span></span> <span data-ttu-id="05098-311">Skopiuj i wklej identyfikator URI nagłówka lokalizacji do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="05098-311">Copy and paste the location header URI into the browser.</span></span>

<span data-ttu-id="05098-312">Aby przetestować w programie Poster:</span><span class="sxs-lookup"><span data-stu-id="05098-312">To test in Postman:</span></span>

* <span data-ttu-id="05098-313">Wybierz kartę **nagłówki** w okienku **odpowiedź** .</span><span class="sxs-lookup"><span data-stu-id="05098-313">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="05098-314">Skopiuj wartość nagłówka **lokalizacji** :</span><span class="sxs-lookup"><span data-stu-id="05098-314">Copy the **Location** header value:</span></span>

  ![Karta nagłówki w konsoli programu Poster](first-web-api/_static/3/create.png)

* <span data-ttu-id="05098-316">Ustaw metodę HTTP na `GET` .</span><span class="sxs-lookup"><span data-stu-id="05098-316">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="05098-317">Ustaw identyfikator URI na `https://localhost:<port>/api/TodoItems/1` .</span><span class="sxs-lookup"><span data-stu-id="05098-317">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="05098-318">Na przykład `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="05098-318">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="05098-319">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="05098-319">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="05098-320">Badanie metod GET</span><span class="sxs-lookup"><span data-stu-id="05098-320">Examine the GET methods</span></span>

<span data-ttu-id="05098-321">Zaimplementowane są dwa punkty końcowe GET:</span><span class="sxs-lookup"><span data-stu-id="05098-321">Two GET endpoints are implemented:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="05098-322">Przetestuj aplikację, wywołując dwa punkty końcowe z przeglądarki lub wpisu.</span><span class="sxs-lookup"><span data-stu-id="05098-322">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="05098-323">Przykład:</span><span class="sxs-lookup"><span data-stu-id="05098-323">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="05098-324">Odpowiedź podobna do poniższego jest generowana przez wywołanie `GetTodoItems` :</span><span class="sxs-lookup"><span data-stu-id="05098-324">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="05098-325">Test get przy użyciu programu Poster</span><span class="sxs-lookup"><span data-stu-id="05098-325">Test Get with Postman</span></span>

* <span data-ttu-id="05098-326">Utwórz nowe żądanie.</span><span class="sxs-lookup"><span data-stu-id="05098-326">Create a new request.</span></span>
* <span data-ttu-id="05098-327">Ustaw metodę HTTP, aby **uzyskać**.</span><span class="sxs-lookup"><span data-stu-id="05098-327">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="05098-328">Ustaw identyfikator URI żądania na `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="05098-328">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="05098-329">Na przykład `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="05098-329">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="05098-330">Ustaw **dwa widoki okienka** w programie Poster.</span><span class="sxs-lookup"><span data-stu-id="05098-330">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="05098-331">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="05098-331">Select **Send**.</span></span>

<span data-ttu-id="05098-332">Ta aplikacja używa bazy danych w pamięci.</span><span class="sxs-lookup"><span data-stu-id="05098-332">This app uses an in-memory database.</span></span> <span data-ttu-id="05098-333">Jeśli aplikacja zostanie zatrzymana i uruchomiona, poprzednie żądanie GET nie zwróci żadnych danych.</span><span class="sxs-lookup"><span data-stu-id="05098-333">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="05098-334">Jeśli nie zostaną zwrócone żadne dane, [Opublikuj](#post) dane w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="05098-334">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="05098-335">Ścieżki routingu i adresów URL</span><span class="sxs-lookup"><span data-stu-id="05098-335">Routing and URL paths</span></span>

<span data-ttu-id="05098-336">Ten [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) atrybut oznacza metodę, która reaguje na żądanie HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="05098-336">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="05098-337">Ścieżka adresu URL dla każdej metody jest zbudowana w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="05098-337">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="05098-338">Zacznij od ciągu szablonu w `Route` atrybucie kontrolera:</span><span class="sxs-lookup"><span data-stu-id="05098-338">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="05098-339">Zastąp `[controller]` nazwą kontrolera, którą Konwencją jest nazwa klasy kontrolera minus sufiks "Controller".</span><span class="sxs-lookup"><span data-stu-id="05098-339">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="05098-340">Dla tego przykładu nazwa klasy kontrolera to **TodoItems** Controller, więc nazwa kontrolera to "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="05098-340">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="05098-341">W ASP.NET Core [routingu](xref:mvc/controllers/routing) jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="05098-341">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="05098-342">Jeśli `[HttpGet]` atrybut ma szablon trasy (na przykład `[HttpGet("products")]` ), Dodaj go do ścieżki.</span><span class="sxs-lookup"><span data-stu-id="05098-342">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="05098-343">Ten przykład nie używa szablonu.</span><span class="sxs-lookup"><span data-stu-id="05098-343">This sample doesn't use a template.</span></span> <span data-ttu-id="05098-344">Aby uzyskać więcej informacji, zobacz temat [Routing atrybutów z atrybutami http [Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="05098-344">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="05098-345">W poniższej `GetTodoItem` metodzie `"{id}"` jest zmienną zastępczą dla unikatowego identyfikatora elementu do wykonania.</span><span class="sxs-lookup"><span data-stu-id="05098-345">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="05098-346">Gdy `GetTodoItem` jest wywoływana, wartość `"{id}"` w adresie URL jest podawana do metody w jej `id` parametrze.</span><span class="sxs-lookup"><span data-stu-id="05098-346">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="05098-347">Wartości zwracane</span><span class="sxs-lookup"><span data-stu-id="05098-347">Return values</span></span>

<span data-ttu-id="05098-348">Zwracany typ `GetTodoItems` `GetTodoItem` metod i jest [ \<T> typem ActionResult](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="05098-348">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="05098-349">ASP.NET Core automatycznie serializować obiektu do [formatu JSON](https://www.json.org/) i zapisuje kod JSON w treści komunikatu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="05098-349">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="05098-350">Kod odpowiedzi dla tego typu zwracanego to [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), przy założeniu, że nie istnieją Nieobsłużone wyjątki.</span><span class="sxs-lookup"><span data-stu-id="05098-350">The response code for this return type is [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="05098-351">Nieobsłużone wyjątki są tłumaczone na błędy 5xx.</span><span class="sxs-lookup"><span data-stu-id="05098-351">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="05098-352">`ActionResult` typy zwracane mogą reprezentować szeroką gamę kodów stanu HTTP.</span><span class="sxs-lookup"><span data-stu-id="05098-352">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="05098-353">Na przykład `GetTodoItem` może zwracać dwie różne wartości stanu:</span><span class="sxs-lookup"><span data-stu-id="05098-353">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="05098-354">Jeśli żaden element nie jest zgodny z żądanym IDENTYFIKATORem, metoda zwraca kod błędu [stanu 404](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> .</span><span class="sxs-lookup"><span data-stu-id="05098-354">If no item matches the requested ID, the method returns a [404 status](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="05098-355">W przeciwnym razie metoda zwraca 200 z treścią odpowiedzi JSON.</span><span class="sxs-lookup"><span data-stu-id="05098-355">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="05098-356">Zwracanie `item` wyników w odpowiedzi HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="05098-356">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="05098-357">Metoda PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="05098-357">The PutTodoItem method</span></span>

<span data-ttu-id="05098-358">Przeanalizuj metodę `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="05098-358">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="05098-359">`PutTodoItem` jest podobny do `PostTodoItem` , z tą różnicą, że używa protokołu HTTP Put.</span><span class="sxs-lookup"><span data-stu-id="05098-359">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="05098-360">Odpowiedź to [204 (brak zawartości)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="05098-360">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="05098-361">Zgodnie ze specyfikacją protokołu HTTP żądanie PUT wymaga, aby klient wysłał całą zaktualizowaną jednostkę, a nie tylko te zmiany.</span><span class="sxs-lookup"><span data-stu-id="05098-361">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="05098-362">Aby zapewnić obsługę częściowych aktualizacji, użyj [poprawki http](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="05098-362">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="05098-363">Jeśli wystąpi błąd podczas wywoływania `PutTodoItem` , wywołaj, `GET` Aby upewnić się, że w bazie danych znajduje się element.</span><span class="sxs-lookup"><span data-stu-id="05098-363">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="05098-364">Testowanie metody PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="05098-364">Test the PutTodoItem method</span></span>

<span data-ttu-id="05098-365">Ten przykład korzysta z bazy danych w pamięci, która musi zostać zainicjowana za każdym razem, gdy aplikacja zostanie uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="05098-365">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="05098-366">Przed wykonaniem wywołania PUT musi istnieć element w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="05098-366">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="05098-367">Przed wykonaniem wywołania PUT należy wywołać metodę GET, aby upewnić się, że istnieje element w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="05098-367">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="05098-368">Zaktualizuj element do wykonania o identyfikatorze 1 i ustaw jego nazwę na `"feed fish"` :</span><span class="sxs-lookup"><span data-stu-id="05098-368">Update the to-do item that has Id = 1 and set its name to `"feed fish"`:</span></span>

```json
  {
    "Id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="05098-369">Na poniższej ilustracji przedstawiono aktualizację programu Poster:</span><span class="sxs-lookup"><span data-stu-id="05098-369">The following image shows the Postman update:</span></span>

![Konsola programu Poster pokazująca odpowiedź 204 (brak zawartości)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="05098-371">Metoda DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="05098-371">The DeleteTodoItem method</span></span>

<span data-ttu-id="05098-372">Przeanalizuj metodę `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="05098-372">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="05098-373">Testowanie metody DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="05098-373">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="05098-374">Użyj programu Poster, aby usunąć element do wykonania:</span><span class="sxs-lookup"><span data-stu-id="05098-374">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="05098-375">Ustaw metodę na `DELETE` .</span><span class="sxs-lookup"><span data-stu-id="05098-375">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="05098-376">Ustaw identyfikator URI obiektu do usunięcia (na przykład `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="05098-376">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="05098-377">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="05098-377">Select **Send**.</span></span>

<a name="over-post-v5"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="05098-378">Zapobiegaj za pośrednictwem księgowania</span><span class="sxs-lookup"><span data-stu-id="05098-378">Prevent over-posting</span></span>

<span data-ttu-id="05098-379">Obecnie Przykładowa aplikacja uwidacznia cały `TodoItem` obiekt.</span><span class="sxs-lookup"><span data-stu-id="05098-379">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="05098-380">Aplikacje produkcyjne zwykle ograniczają dane wejściowe i zwracane przy użyciu podzestawu modelu.</span><span class="sxs-lookup"><span data-stu-id="05098-380">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="05098-381">Istnieje wiele powodów związanych z tym, a zabezpieczenia są głównymi.</span><span class="sxs-lookup"><span data-stu-id="05098-381">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="05098-382">Podzestaw modelu jest zwykle określany jako obiekt Transfer danych (DTO), model wejściowy lub model widoku.</span><span class="sxs-lookup"><span data-stu-id="05098-382">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="05098-383">**DTO** jest używany w tym artykule.</span><span class="sxs-lookup"><span data-stu-id="05098-383">**DTO** is used in this article.</span></span>

<span data-ttu-id="05098-384">DTO może służyć do:</span><span class="sxs-lookup"><span data-stu-id="05098-384">A DTO may be used to:</span></span>

* <span data-ttu-id="05098-385">Zablokuj nadmierne księgowanie.</span><span class="sxs-lookup"><span data-stu-id="05098-385">Prevent over-posting.</span></span>
* <span data-ttu-id="05098-386">Ukryj właściwości, które nie powinny być wyświetlane dla klientów.</span><span class="sxs-lookup"><span data-stu-id="05098-386">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="05098-387">Pomiń niektóre właściwości, aby zmniejszyć rozmiar ładunku.</span><span class="sxs-lookup"><span data-stu-id="05098-387">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="05098-388">Spłaszcz wykresy obiektów zawierające obiekty zagnieżdżone.</span><span class="sxs-lookup"><span data-stu-id="05098-388">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="05098-389">Spłaszczone wykresy obiektów mogą być wygodniejsze dla klientów.</span><span class="sxs-lookup"><span data-stu-id="05098-389">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="05098-390">Aby zademonstrować podejście DTO, zaktualizuj `TodoItem` klasę w celu uwzględnienia pola tajnego:</span><span class="sxs-lookup"><span data-stu-id="05098-390">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=8)]

<span data-ttu-id="05098-391">Pole tajne musi być ukryte w tej aplikacji, ale aplikacja administracyjna mogła ją uwidocznić.</span><span class="sxs-lookup"><span data-stu-id="05098-391">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="05098-392">Sprawdź, czy można opublikować i pobrać pole tajne.</span><span class="sxs-lookup"><span data-stu-id="05098-392">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="05098-393">Utwórz model DTO:</span><span class="sxs-lookup"><span data-stu-id="05098-393">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="05098-394">Zaktualizuj, `TodoItemsController` Aby użyć `TodoItemDTO` :</span><span class="sxs-lookup"><span data-stu-id="05098-394">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="05098-395">Upewnij się, że nie można opublikować lub pobrać pola tajnego.</span><span class="sxs-lookup"><span data-stu-id="05098-395">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="05098-396">Wywoływanie interfejsu API sieci Web przy użyciu języka JavaScript</span><span class="sxs-lookup"><span data-stu-id="05098-396">Call the web API with JavaScript</span></span>

<span data-ttu-id="05098-397">Zobacz [Samouczek: wywoływanie interfejsu API sieci web ASP.NET Core przy użyciu języka JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="05098-397">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="05098-398">Z tego samouczka dowiesz się, jak wykonywać następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="05098-398">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="05098-399">Utwórz projekt interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="05098-399">Create a web API project.</span></span>
> * <span data-ttu-id="05098-400">Dodaj klasę modelu i kontekst bazy danych.</span><span class="sxs-lookup"><span data-stu-id="05098-400">Add a model class and a database context.</span></span>
> * <span data-ttu-id="05098-401">Tworzy szkielet kontrolera z metodami CRUD.</span><span class="sxs-lookup"><span data-stu-id="05098-401">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="05098-402">Skonfiguruj Routing, ścieżki URL i wartości zwracane.</span><span class="sxs-lookup"><span data-stu-id="05098-402">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="05098-403">Wywołaj interfejs API sieci Web za pomocą programu Poster.</span><span class="sxs-lookup"><span data-stu-id="05098-403">Call the web API with Postman.</span></span>

<span data-ttu-id="05098-404">Na końcu znajduje się internetowy interfejs API, który może zarządzać elementami do wykonania przechowywanymi w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="05098-404">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="05098-405">Omówienie</span><span class="sxs-lookup"><span data-stu-id="05098-405">Overview</span></span>

<span data-ttu-id="05098-406">Ten samouczek tworzy następujący interfejs API:</span><span class="sxs-lookup"><span data-stu-id="05098-406">This tutorial creates the following API:</span></span>

|<span data-ttu-id="05098-407">Interfejs API</span><span class="sxs-lookup"><span data-stu-id="05098-407">API</span></span> | <span data-ttu-id="05098-408">Opis</span><span class="sxs-lookup"><span data-stu-id="05098-408">Description</span></span> | <span data-ttu-id="05098-409">Treść żądania</span><span class="sxs-lookup"><span data-stu-id="05098-409">Request body</span></span> | <span data-ttu-id="05098-410">Treść odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="05098-410">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="05098-411">Pobierz wszystkie elementy do wykonania</span><span class="sxs-lookup"><span data-stu-id="05098-411">Get all to-do items</span></span> | <span data-ttu-id="05098-412">Brak</span><span class="sxs-lookup"><span data-stu-id="05098-412">None</span></span> | <span data-ttu-id="05098-413">Tablica elementów do wykonania</span><span class="sxs-lookup"><span data-stu-id="05098-413">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="05098-414">Pobieranie elementu według identyfikatora</span><span class="sxs-lookup"><span data-stu-id="05098-414">Get an item by ID</span></span> | <span data-ttu-id="05098-415">Brak</span><span class="sxs-lookup"><span data-stu-id="05098-415">None</span></span> | <span data-ttu-id="05098-416">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="05098-416">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="05098-417">Dodaj nowy element</span><span class="sxs-lookup"><span data-stu-id="05098-417">Add a new item</span></span> | <span data-ttu-id="05098-418">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="05098-418">To-do item</span></span> | <span data-ttu-id="05098-419">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="05098-419">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="05098-420">Aktualizowanie istniejącego elementu &nbsp;</span><span class="sxs-lookup"><span data-stu-id="05098-420">Update an existing item &nbsp;</span></span> | <span data-ttu-id="05098-421">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="05098-421">To-do item</span></span> | <span data-ttu-id="05098-422">Brak</span><span class="sxs-lookup"><span data-stu-id="05098-422">None</span></span> |
|<span data-ttu-id="05098-423">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="05098-423">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="05098-424">Usuń element &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="05098-424">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="05098-425">Brak</span><span class="sxs-lookup"><span data-stu-id="05098-425">None</span></span> | <span data-ttu-id="05098-426">Brak</span><span class="sxs-lookup"><span data-stu-id="05098-426">None</span></span>|

<span data-ttu-id="05098-427">Na poniższym diagramie przedstawiono projekt aplikacji.</span><span class="sxs-lookup"><span data-stu-id="05098-427">The following diagram shows the design of the app.</span></span>

![Klient jest reprezentowany przez pole po lewej stronie.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="05098-433">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="05098-433">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="05098-434">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="05098-434">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="05098-435">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="05098-435">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="05098-436">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="05098-436">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="05098-437">Tworzenie projektu sieci Web</span><span class="sxs-lookup"><span data-stu-id="05098-437">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="05098-438">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="05098-438">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="05098-439">Z menu **plik** wybierz pozycję **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="05098-439">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="05098-440">Wybierz szablon **aplikacja sieci Web ASP.NET Core** a następnie kliknij przycisk **dalej**.</span><span class="sxs-lookup"><span data-stu-id="05098-440">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="05098-441">Nazwij projekt *TodoApi* i kliknij pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="05098-441">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="05098-442">W oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** upewnij się, że wybrano opcję **.net Core** i **ASP.NET Core 3,1** .</span><span class="sxs-lookup"><span data-stu-id="05098-442">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="05098-443">Wybierz szablon **interfejsu API** i kliknij przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="05098-443">Select the **API** template and click **Create**.</span></span>

![Okno dialogowe programu VS New Project](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="05098-445">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="05098-445">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="05098-446">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="05098-446">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="05098-447">Zmień katalog ( `cd` ) do folderu, który będzie zawierać folder projektu.</span><span class="sxs-lookup"><span data-stu-id="05098-447">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="05098-448">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="05098-448">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="05098-449">Gdy zostanie wyświetlone okno dialogowe z pytaniem, czy chcesz dodać wymagane zasoby do projektu, wybierz opcję **tak**.</span><span class="sxs-lookup"><span data-stu-id="05098-449">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="05098-450">Poprzednie polecenia:</span><span class="sxs-lookup"><span data-stu-id="05098-450">The preceding commands:</span></span>

  * <span data-ttu-id="05098-451">Tworzy nowy projekt internetowego interfejsu API i otwiera go w Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="05098-451">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="05098-452">Dodaje pakiety NuGet, które są wymagane w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="05098-452">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="05098-453">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="05098-453">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="05098-454">Wybierz pozycję **plik** > **nowe rozwiązanie**.</span><span class="sxs-lookup"><span data-stu-id="05098-454">Select **File** > **New Solution**.</span></span>

  ![macOS nowe rozwiązanie](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="05098-456">W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz **.NET Core** pozycję  >  **App**  >  **interfejs API** aplikacji .NET Core  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="05098-456">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="05098-457">W wersji 8,6 lub nowszej wybierz pozycję **Web and Console**  >  **App**  >  **interfejs API**  >  **Next** aplikacji sieci Web i konsoli.</span><span class="sxs-lookup"><span data-stu-id="05098-457">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![Wybór szablonu interfejsu API macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="05098-459">W oknie dialogowym **Konfigurowanie nowego interfejsu API sieci Web ASP.NET Core** wybierz najnowszą platformę **docelową**.NET Core 3. x.</span><span class="sxs-lookup"><span data-stu-id="05098-459">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="05098-460">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="05098-460">Select **Next**.</span></span>

* <span data-ttu-id="05098-461">Wprowadź *TodoApi* jako **nazwę projektu** , a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="05098-461">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![okno dialogowe konfiguracji](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="05098-463">Otwórz Terminal poleceń w folderze projektu i uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="05098-463">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="05098-464">Testowanie interfejsu API</span><span class="sxs-lookup"><span data-stu-id="05098-464">Test the API</span></span>

<span data-ttu-id="05098-465">Szablon projektu tworzy `WeatherForecast` interfejs API.</span><span class="sxs-lookup"><span data-stu-id="05098-465">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="05098-466">Wywołaj `Get` metodę z przeglądarki, aby przetestować aplikację.</span><span class="sxs-lookup"><span data-stu-id="05098-466">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="05098-467">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="05098-467">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="05098-468">Naciśnij klawisze CTRL + F5, aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="05098-468">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="05098-469">Program Visual Studio uruchamia przeglądarkę i przechodzi do `https://localhost:<port>/WeatherForecast` lokalizacji, gdzie `<port>` jest losowo wybierany numer portu.</span><span class="sxs-lookup"><span data-stu-id="05098-469">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="05098-470">Jeśli zostanie wyświetlone okno dialogowe z pytaniem, czy należy zaufać certyfikatowi IIS Express, wybierz pozycję **tak**.</span><span class="sxs-lookup"><span data-stu-id="05098-470">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="05098-471">W wyświetlonym oknie dialogowym **ostrzeżenia o zabezpieczeniach** wybierz pozycję **tak**.</span><span class="sxs-lookup"><span data-stu-id="05098-471">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="05098-472">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="05098-472">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="05098-473">Naciśnij klawisze CTRL + F5, aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="05098-473">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="05098-474">W przeglądarce przejdź do następującego adresu URL: `https://localhost:5001/WeatherForecast` .</span><span class="sxs-lookup"><span data-stu-id="05098-474">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="05098-475">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="05098-475">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="05098-476">Wybierz pozycję **Uruchom**  >  **Rozpocznij debugowanie** , aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="05098-476">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="05098-477">Visual Studio dla komputerów Mac uruchamia przeglądarkę i nawiguje do `https://localhost:<port>` , gdzie `<port>` jest losowo wybranym numerem portu.</span><span class="sxs-lookup"><span data-stu-id="05098-477">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="05098-478">Zwracany jest błąd HTTP 404 (nie znaleziono).</span><span class="sxs-lookup"><span data-stu-id="05098-478">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="05098-479">Dołącz `/WeatherForecast` do adresu URL (Zmień adres URL na `https://localhost:<port>/WeatherForecast` ).</span><span class="sxs-lookup"><span data-stu-id="05098-479">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="05098-480">Zwracany jest kod JSON podobny do następującego:</span><span class="sxs-lookup"><span data-stu-id="05098-480">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="05098-481">Dodaj klasę modelu</span><span class="sxs-lookup"><span data-stu-id="05098-481">Add a model class</span></span>

<span data-ttu-id="05098-482">*Model* to zestaw klas, które reprezentują dane zarządzane przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="05098-482">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="05098-483">Model tej aplikacji jest pojedynczą `TodoItem` klasą.</span><span class="sxs-lookup"><span data-stu-id="05098-483">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="05098-484">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="05098-484">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="05098-485">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt.</span><span class="sxs-lookup"><span data-stu-id="05098-485">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="05098-486">Wybierz pozycję **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="05098-486">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="05098-487">Nazwij folder *Models* .</span><span class="sxs-lookup"><span data-stu-id="05098-487">Name the folder *Models*.</span></span>

* <span data-ttu-id="05098-488">Kliknij prawym przyciskiem myszy *Models* folder i wybierz polecenie **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="05098-488">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="05098-489">Nadaj klasie nazwę *TodoItem* i wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="05098-489">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="05098-490">Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="05098-490">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="05098-491">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="05098-491">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="05098-492">Dodaj folder o nazwie *Models* .</span><span class="sxs-lookup"><span data-stu-id="05098-492">Add a folder named *Models*.</span></span>

* <span data-ttu-id="05098-493">Dodaj `TodoItem` do *Models* folderu klasę o następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="05098-493">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="05098-494">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="05098-494">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="05098-495">Kliknij prawym przyciskiem myszy projekt.</span><span class="sxs-lookup"><span data-stu-id="05098-495">Right-click the project.</span></span> <span data-ttu-id="05098-496">Wybierz pozycję **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="05098-496">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="05098-497">Nazwij folder *Models* .</span><span class="sxs-lookup"><span data-stu-id="05098-497">Name the folder *Models*.</span></span>

  ![Nowy folder](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="05098-499">Kliknij prawym przyciskiem myszy *Models* folder, a następnie wybierz pozycję **Dodaj** > **nowy plik** > **ogólna** > **pusta Klasa**.</span><span class="sxs-lookup"><span data-stu-id="05098-499">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="05098-500">Nazwij klasę *TodoItem*, a następnie kliknij pozycję **New (nowy**).</span><span class="sxs-lookup"><span data-stu-id="05098-500">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="05098-501">Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="05098-501">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="05098-502">`Id`Właściwość działa jako unikatowy klucz w relacyjnej bazie danych.</span><span class="sxs-lookup"><span data-stu-id="05098-502">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="05098-503">Klasy modelu mogą przejść do dowolnego miejsca w projekcie, ale *Models* folder jest używany przez Konwencję.</span><span class="sxs-lookup"><span data-stu-id="05098-503">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="05098-504">Dodawanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="05098-504">Add a database context</span></span>

<span data-ttu-id="05098-505">*Kontekst bazy danych* jest główną klasą, która koordynuje Entity Framework funkcji dla modelu danych.</span><span class="sxs-lookup"><span data-stu-id="05098-505">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="05098-506">Ta klasa jest tworzona przez wyprowadzanie z `Microsoft.EntityFrameworkCore.DbContext` klasy.</span><span class="sxs-lookup"><span data-stu-id="05098-506">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="05098-507">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="05098-507">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="05098-508">Dodawanie pakietów NuGet</span><span class="sxs-lookup"><span data-stu-id="05098-508">Add NuGet packages</span></span>

* <span data-ttu-id="05098-509">W menu **Narzędzia** wybierz pozycję **menedżer pakietów NuGet > zarządzanie pakietami NuGet dla rozwiązania**.</span><span class="sxs-lookup"><span data-stu-id="05098-509">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="05098-510">Wybierz kartę **Przeglądaj** , a następnie w polu wyszukiwania wprowadź ciąg **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="05098-510">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="05098-511">W lewym okienku wybierz pozycję **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="05098-511">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="05098-512">Zaznacz pole wyboru **projekt** w prawym okienku, a następnie wybierz pozycję **Zainstaluj**.</span><span class="sxs-lookup"><span data-stu-id="05098-512">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="05098-513">Użyj powyższych instrukcji, aby dodać pakiet NuGet **Microsoft. EntityFrameworkCore. inMemory** .</span><span class="sxs-lookup"><span data-stu-id="05098-513">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

![Menedżer pakietów NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="05098-515">Dodawanie kontekstu bazy danych TodoContext</span><span class="sxs-lookup"><span data-stu-id="05098-515">Add the TodoContext database context</span></span>

* <span data-ttu-id="05098-516">Kliknij prawym przyciskiem myszy *Models* folder i wybierz polecenie **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="05098-516">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="05098-517">Nadaj klasie nazwę *TodoContext* i kliknij przycisk **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="05098-517">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="05098-518">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="05098-518">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="05098-519">Dodaj `TodoContext` klasę do *Models* folderu.</span><span class="sxs-lookup"><span data-stu-id="05098-519">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="05098-520">Wprowadź następujący kod:</span><span class="sxs-lookup"><span data-stu-id="05098-520">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="05098-521">Rejestrowanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="05098-521">Register the database context</span></span>

<span data-ttu-id="05098-522">W ASP.NET Core usługi, takie jak kontekst bazy danych, muszą być zarejestrowane z kontenerem [iniekcji zależności (di)](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="05098-522">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="05098-523">Kontener udostępnia usługę kontrolerom.</span><span class="sxs-lookup"><span data-stu-id="05098-523">The container provides the service to controllers.</span></span>

<span data-ttu-id="05098-524">Zaktualizuj *Startup.cs* o następujący wyróżniony kod:</span><span class="sxs-lookup"><span data-stu-id="05098-524">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="05098-525">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="05098-525">The preceding code:</span></span>

* <span data-ttu-id="05098-526">Usuwa nieużywane `using` deklaracje.</span><span class="sxs-lookup"><span data-stu-id="05098-526">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="05098-527">Dodaje kontekst bazy danych do kontenera DI.</span><span class="sxs-lookup"><span data-stu-id="05098-527">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="05098-528">Określa, że kontekst bazy danych będzie używać bazy danych w pamięci.</span><span class="sxs-lookup"><span data-stu-id="05098-528">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="05098-529">Tworzenie szkieletu kontrolera</span><span class="sxs-lookup"><span data-stu-id="05098-529">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="05098-530">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="05098-530">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="05098-531">Kliknij prawym przyciskiem myszy folder *controllers* .</span><span class="sxs-lookup"><span data-stu-id="05098-531">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="05098-532">Wybierz pozycję **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="05098-532">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="05098-533">Wybierz pozycję **kontroler interfejsu API z akcjami, używając Entity Framework**, a następnie wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="05098-533">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="05098-534">Na stronie **Dodawanie kontrolera interfejsu API z akcjami przy użyciu Entity Framework** dialogowego:</span><span class="sxs-lookup"><span data-stu-id="05098-534">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="05098-535">Wybierz pozycję **TodoItem (TodoApi. Models )** w **klasie model**.</span><span class="sxs-lookup"><span data-stu-id="05098-535">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="05098-536">Wybierz pozycję **TodoContext (TodoApi. Models )** w **klasie kontekstu danych**.</span><span class="sxs-lookup"><span data-stu-id="05098-536">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="05098-537">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="05098-537">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="05098-538">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="05098-538">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="05098-539">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="05098-539">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="05098-540">Poprzednie polecenia:</span><span class="sxs-lookup"><span data-stu-id="05098-540">The preceding commands:</span></span>

* <span data-ttu-id="05098-541">Dodaj pakiety NuGet wymagane do tworzenia szkieletów.</span><span class="sxs-lookup"><span data-stu-id="05098-541">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="05098-542">Instaluje aparat tworzenia szkieletu ( `dotnet-aspnet-codegenerator` ).</span><span class="sxs-lookup"><span data-stu-id="05098-542">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="05098-543">Szkielety `TodoItemsController` .</span><span class="sxs-lookup"><span data-stu-id="05098-543">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="05098-544">Wygenerowany kod:</span><span class="sxs-lookup"><span data-stu-id="05098-544">The generated code:</span></span>

* <span data-ttu-id="05098-545">Oznacza klasę [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atrybutem.</span><span class="sxs-lookup"><span data-stu-id="05098-545">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="05098-546">Ten atrybut wskazuje, że kontroler odpowiada na żądania interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="05098-546">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="05098-547">Aby uzyskać informacje o określonych zachowaniach, które włącza atrybut, zobacz <xref:web-api/index> .</span><span class="sxs-lookup"><span data-stu-id="05098-547">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="05098-548">Używa funkcji DI do iniekcji kontekstu bazy danych ( `TodoContext` ) do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="05098-548">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="05098-549">Kontekst bazy danych jest używany w każdej z metod [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) w kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="05098-549">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="05098-550">Szablony ASP.NET Core dla:</span><span class="sxs-lookup"><span data-stu-id="05098-550">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="05098-551">Kontrolery z widokami obejmują `[action]` szablon trasy.</span><span class="sxs-lookup"><span data-stu-id="05098-551">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="05098-552">Kontrolery interfejsu API nie należą `[action]` do szablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="05098-552">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="05098-553">Gdy `[action]` token nie znajduje się w szablonie trasy, nazwa [akcji](xref:mvc/controllers/routing#action) jest wykluczona z trasy.</span><span class="sxs-lookup"><span data-stu-id="05098-553">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="05098-554">Oznacza to, że nazwa metody skojarzonej z akcją nie jest używana w zgodnej trasie.</span><span class="sxs-lookup"><span data-stu-id="05098-554">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="05098-555">Badanie metody PostTodoItem Create</span><span class="sxs-lookup"><span data-stu-id="05098-555">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="05098-556">Zastąp instrukcję return w, `PostTodoItem` Aby użyć operatora [nameof](/dotnet/csharp/language-reference/operators/nameof) :</span><span class="sxs-lookup"><span data-stu-id="05098-556">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="05098-557">Poprzedni kod jest metodą POST protokołu HTTP, jak wskazano w [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) atrybucie.</span><span class="sxs-lookup"><span data-stu-id="05098-557">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="05098-558">Metoda pobiera wartość elementu do wykonania z treści żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="05098-558">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="05098-559">Aby uzyskać więcej informacji, zobacz temat [Routing atrybutów z atrybutami http [Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="05098-559">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="05098-560"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>Metoda:</span><span class="sxs-lookup"><span data-stu-id="05098-560">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="05098-561">W razie powodzenia zwraca kod stanu HTTP 201.</span><span class="sxs-lookup"><span data-stu-id="05098-561">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="05098-562">HTTP 201 to standardowa odpowiedź dla metody POST protokołu HTTP, która tworzy nowy zasób na serwerze.</span><span class="sxs-lookup"><span data-stu-id="05098-562">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="05098-563">Dodaje nagłówek [lokalizacji](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) do odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="05098-563">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="05098-564">`Location`Nagłówek określa [Identyfikator URI](https://developer.mozilla.org/docs/Glossary/URI) nowo utworzonego elementu do wykonania.</span><span class="sxs-lookup"><span data-stu-id="05098-564">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="05098-565">Aby uzyskać więcej informacji, zobacz [10.2.2 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="05098-565">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="05098-566">Odwołuje się do `GetTodoItem` akcji tworzenia `Location` identyfikatora URI nagłówka.</span><span class="sxs-lookup"><span data-stu-id="05098-566">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="05098-567">`nameof`Słowo kluczowe języka C# służy do zapobiegania twardemu kodowaniu nazwy akcji w `CreatedAtAction` wywołaniu.</span><span class="sxs-lookup"><span data-stu-id="05098-567">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="05098-568">Zainstaluj program Poster</span><span class="sxs-lookup"><span data-stu-id="05098-568">Install Postman</span></span>

<span data-ttu-id="05098-569">Ten samouczek używa programu do testowania interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="05098-569">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="05098-570">Zainstaluj program [Poster](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="05098-570">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="05098-571">Uruchom aplikację internetową.</span><span class="sxs-lookup"><span data-stu-id="05098-571">Start the web app.</span></span>
* <span data-ttu-id="05098-572">Uruchom wpis.</span><span class="sxs-lookup"><span data-stu-id="05098-572">Start Postman.</span></span>
* <span data-ttu-id="05098-573">Wyłącz **weryfikację certyfikatu SSL**</span><span class="sxs-lookup"><span data-stu-id="05098-573">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="05098-574">W **File** obszarze > **Ustawienia** pliku (karta **Ogólne** ) Wyłącz **weryfikację certyfikatu SSL**.</span><span class="sxs-lookup"><span data-stu-id="05098-574">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="05098-575">Po przetestowaniu kontrolera ponownie Włącz weryfikację certyfikatu SSL.</span><span class="sxs-lookup"><span data-stu-id="05098-575">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="05098-576">Test PostTodoItem za pomocą programu Poster</span><span class="sxs-lookup"><span data-stu-id="05098-576">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="05098-577">Utwórz nowe żądanie.</span><span class="sxs-lookup"><span data-stu-id="05098-577">Create a new request.</span></span>
* <span data-ttu-id="05098-578">Ustaw metodę HTTP na `POST` .</span><span class="sxs-lookup"><span data-stu-id="05098-578">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="05098-579">Ustaw identyfikator URI na `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="05098-579">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="05098-580">Na przykład `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="05098-580">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="05098-581">Wybierz kartę **Treść**.</span><span class="sxs-lookup"><span data-stu-id="05098-581">Select the **Body** tab.</span></span>
* <span data-ttu-id="05098-582">Wybierz przycisk radiowy **RAW** .</span><span class="sxs-lookup"><span data-stu-id="05098-582">Select the **raw** radio button.</span></span>
* <span data-ttu-id="05098-583">Ustaw typ na **JSON (Application/JSON)**.</span><span class="sxs-lookup"><span data-stu-id="05098-583">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="05098-584">W treści żądania wprowadź kod JSON dla elementu do wykonania:</span><span class="sxs-lookup"><span data-stu-id="05098-584">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="05098-585">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="05098-585">Select **Send**.</span></span>

  ![Ogłoś przy użyciu żądania Create](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a><span data-ttu-id="05098-587">Testowanie identyfikatora URI nagłówka lokalizacji za pomocą programu Poster</span><span class="sxs-lookup"><span data-stu-id="05098-587">Test the location header URI with Postman</span></span>

* <span data-ttu-id="05098-588">Wybierz kartę **nagłówki** w okienku **odpowiedź** .</span><span class="sxs-lookup"><span data-stu-id="05098-588">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="05098-589">Skopiuj wartość nagłówka **lokalizacji** :</span><span class="sxs-lookup"><span data-stu-id="05098-589">Copy the **Location** header value:</span></span>

  ![Karta nagłówki w konsoli programu Poster](first-web-api/_static/3/create.png)

* <span data-ttu-id="05098-591">Ustaw metodę HTTP na `GET` .</span><span class="sxs-lookup"><span data-stu-id="05098-591">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="05098-592">Ustaw identyfikator URI na `https://localhost:<port>/api/TodoItems/1` .</span><span class="sxs-lookup"><span data-stu-id="05098-592">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="05098-593">Na przykład `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="05098-593">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="05098-594">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="05098-594">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="05098-595">Badanie metod GET</span><span class="sxs-lookup"><span data-stu-id="05098-595">Examine the GET methods</span></span>

<span data-ttu-id="05098-596">Te metody implementują dwa punkty końcowe GET:</span><span class="sxs-lookup"><span data-stu-id="05098-596">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="05098-597">Przetestuj aplikację, wywołując dwa punkty końcowe z przeglądarki lub wpisu.</span><span class="sxs-lookup"><span data-stu-id="05098-597">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="05098-598">Przykład:</span><span class="sxs-lookup"><span data-stu-id="05098-598">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="05098-599">Odpowiedź podobna do poniższego jest generowana przez wywołanie `GetTodoItems` :</span><span class="sxs-lookup"><span data-stu-id="05098-599">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="05098-600">Test get przy użyciu programu Poster</span><span class="sxs-lookup"><span data-stu-id="05098-600">Test Get with Postman</span></span>

* <span data-ttu-id="05098-601">Utwórz nowe żądanie.</span><span class="sxs-lookup"><span data-stu-id="05098-601">Create a new request.</span></span>
* <span data-ttu-id="05098-602">Ustaw metodę HTTP, aby **uzyskać**.</span><span class="sxs-lookup"><span data-stu-id="05098-602">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="05098-603">Ustaw identyfikator URI żądania na `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="05098-603">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="05098-604">Na przykład `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="05098-604">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="05098-605">Ustaw **dwa widoki okienka** w programie Poster.</span><span class="sxs-lookup"><span data-stu-id="05098-605">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="05098-606">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="05098-606">Select **Send**.</span></span>

<span data-ttu-id="05098-607">Ta aplikacja używa bazy danych w pamięci.</span><span class="sxs-lookup"><span data-stu-id="05098-607">This app uses an in-memory database.</span></span> <span data-ttu-id="05098-608">Jeśli aplikacja zostanie zatrzymana i uruchomiona, poprzednie żądanie GET nie zwróci żadnych danych.</span><span class="sxs-lookup"><span data-stu-id="05098-608">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="05098-609">Jeśli nie zostaną zwrócone żadne dane, [Opublikuj](#post) dane w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="05098-609">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="05098-610">Ścieżki routingu i adresów URL</span><span class="sxs-lookup"><span data-stu-id="05098-610">Routing and URL paths</span></span>

<span data-ttu-id="05098-611">Ten [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) atrybut oznacza metodę, która reaguje na żądanie HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="05098-611">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="05098-612">Ścieżka adresu URL dla każdej metody jest zbudowana w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="05098-612">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="05098-613">Zacznij od ciągu szablonu w `Route` atrybucie kontrolera:</span><span class="sxs-lookup"><span data-stu-id="05098-613">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="05098-614">Zastąp `[controller]` nazwą kontrolera, którą Konwencją jest nazwa klasy kontrolera minus sufiks "Controller".</span><span class="sxs-lookup"><span data-stu-id="05098-614">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="05098-615">Dla tego przykładu nazwa klasy kontrolera to **TodoItems** Controller, więc nazwa kontrolera to "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="05098-615">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="05098-616">W ASP.NET Core [routingu](xref:mvc/controllers/routing) jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="05098-616">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="05098-617">Jeśli `[HttpGet]` atrybut ma szablon trasy (na przykład `[HttpGet("products")]` ), Dodaj go do ścieżki.</span><span class="sxs-lookup"><span data-stu-id="05098-617">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="05098-618">Ten przykład nie używa szablonu.</span><span class="sxs-lookup"><span data-stu-id="05098-618">This sample doesn't use a template.</span></span> <span data-ttu-id="05098-619">Aby uzyskać więcej informacji, zobacz temat [Routing atrybutów z atrybutami http [Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="05098-619">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="05098-620">W poniższej `GetTodoItem` metodzie `"{id}"` jest zmienną zastępczą dla unikatowego identyfikatora elementu do wykonania.</span><span class="sxs-lookup"><span data-stu-id="05098-620">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="05098-621">Gdy `GetTodoItem` jest wywoływana, wartość `"{id}"` w adresie URL jest podawana do metody w jej `id` parametrze.</span><span class="sxs-lookup"><span data-stu-id="05098-621">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="05098-622">Wartości zwracane</span><span class="sxs-lookup"><span data-stu-id="05098-622">Return values</span></span> 

<span data-ttu-id="05098-623">Zwracany typ `GetTodoItems` `GetTodoItem` metod i jest [ \<T> typem ActionResult](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="05098-623">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="05098-624">ASP.NET Core automatycznie serializować obiektu do [formatu JSON](https://www.json.org/) i zapisuje kod JSON w treści komunikatu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="05098-624">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="05098-625">Kod odpowiedzi dla tego typu zwracanego to 200, przy założeniu, że nie istnieją Nieobsłużone wyjątki.</span><span class="sxs-lookup"><span data-stu-id="05098-625">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="05098-626">Nieobsłużone wyjątki są tłumaczone na błędy 5xx.</span><span class="sxs-lookup"><span data-stu-id="05098-626">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="05098-627">`ActionResult` typy zwracane mogą reprezentować szeroką gamę kodów stanu HTTP.</span><span class="sxs-lookup"><span data-stu-id="05098-627">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="05098-628">Na przykład `GetTodoItem` może zwracać dwie różne wartości stanu:</span><span class="sxs-lookup"><span data-stu-id="05098-628">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="05098-629">Jeśli żaden element nie jest zgodny z żądanym IDENTYFIKATORem, metoda zwróci <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> Kod błędu 404.</span><span class="sxs-lookup"><span data-stu-id="05098-629">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="05098-630">W przeciwnym razie metoda zwraca 200 z treścią odpowiedzi JSON.</span><span class="sxs-lookup"><span data-stu-id="05098-630">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="05098-631">Zwracanie `item` wyników w odpowiedzi HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="05098-631">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="05098-632">Metoda PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="05098-632">The PutTodoItem method</span></span>

<span data-ttu-id="05098-633">Przeanalizuj metodę `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="05098-633">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="05098-634">`PutTodoItem` jest podobny do `PostTodoItem` , z tą różnicą, że używa protokołu HTTP Put.</span><span class="sxs-lookup"><span data-stu-id="05098-634">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="05098-635">Odpowiedź to [204 (brak zawartości)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="05098-635">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="05098-636">Zgodnie ze specyfikacją protokołu HTTP żądanie PUT wymaga, aby klient wysłał całą zaktualizowaną jednostkę, a nie tylko te zmiany.</span><span class="sxs-lookup"><span data-stu-id="05098-636">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="05098-637">Aby zapewnić obsługę częściowych aktualizacji, użyj [poprawki http](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="05098-637">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="05098-638">Jeśli wystąpi błąd podczas wywoływania `PutTodoItem` , wywołaj, `GET` Aby upewnić się, że w bazie danych znajduje się element.</span><span class="sxs-lookup"><span data-stu-id="05098-638">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="05098-639">Testowanie metody PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="05098-639">Test the PutTodoItem method</span></span>

<span data-ttu-id="05098-640">Ten przykład korzysta z bazy danych w pamięci, która musi zostać zainicjowana za każdym razem, gdy aplikacja zostanie uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="05098-640">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="05098-641">Przed wykonaniem wywołania PUT musi istnieć element w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="05098-641">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="05098-642">Przed wykonaniem wywołania PUT należy wywołać metodę GET, aby upewnić się, że istnieje element w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="05098-642">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="05098-643">Zaktualizuj element do wykonania o identyfikatorze 1 i ustaw jego nazwę na "Źródło danych":</span><span class="sxs-lookup"><span data-stu-id="05098-643">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="05098-644">Na poniższej ilustracji przedstawiono aktualizację programu Poster:</span><span class="sxs-lookup"><span data-stu-id="05098-644">The following image shows the Postman update:</span></span>

![Konsola programu Poster pokazująca odpowiedź 204 (brak zawartości)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="05098-646">Metoda DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="05098-646">The DeleteTodoItem method</span></span>

<span data-ttu-id="05098-647">Przeanalizuj metodę `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="05098-647">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="05098-648">Testowanie metody DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="05098-648">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="05098-649">Użyj programu Poster, aby usunąć element do wykonania:</span><span class="sxs-lookup"><span data-stu-id="05098-649">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="05098-650">Ustaw metodę na `DELETE` .</span><span class="sxs-lookup"><span data-stu-id="05098-650">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="05098-651">Ustaw identyfikator URI obiektu do usunięcia (na przykład `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="05098-651">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="05098-652">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="05098-652">Select **Send**.</span></span>

<a name="over-post"></a>
<a name="over-post-v3"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="05098-653">Zapobiegaj za pośrednictwem księgowania</span><span class="sxs-lookup"><span data-stu-id="05098-653">Prevent over-posting</span></span>

<span data-ttu-id="05098-654">Obecnie Przykładowa aplikacja uwidacznia cały `TodoItem` obiekt.</span><span class="sxs-lookup"><span data-stu-id="05098-654">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="05098-655">Aplikacje produkcyjne zwykle ograniczają dane wejściowe i zwracane przy użyciu podzestawu modelu.</span><span class="sxs-lookup"><span data-stu-id="05098-655">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="05098-656">Istnieje wiele powodów związanych z tym, a zabezpieczenia są głównymi.</span><span class="sxs-lookup"><span data-stu-id="05098-656">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="05098-657">Podzestaw modelu jest zwykle określany jako obiekt Transfer danych (DTO), model wejściowy lub model widoku.</span><span class="sxs-lookup"><span data-stu-id="05098-657">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="05098-658">**DTO** jest używany w tym artykule.</span><span class="sxs-lookup"><span data-stu-id="05098-658">**DTO** is used in this article.</span></span>

<span data-ttu-id="05098-659">DTO może służyć do:</span><span class="sxs-lookup"><span data-stu-id="05098-659">A DTO may be used to:</span></span>

* <span data-ttu-id="05098-660">Zablokuj nadmierne księgowanie.</span><span class="sxs-lookup"><span data-stu-id="05098-660">Prevent over-posting.</span></span>
* <span data-ttu-id="05098-661">Ukryj właściwości, które nie powinny być wyświetlane dla klientów.</span><span class="sxs-lookup"><span data-stu-id="05098-661">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="05098-662">Pomiń niektóre właściwości, aby zmniejszyć rozmiar ładunku.</span><span class="sxs-lookup"><span data-stu-id="05098-662">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="05098-663">Spłaszcz wykresy obiektów zawierające obiekty zagnieżdżone.</span><span class="sxs-lookup"><span data-stu-id="05098-663">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="05098-664">Spłaszczone wykresy obiektów mogą być wygodniejsze dla klientów.</span><span class="sxs-lookup"><span data-stu-id="05098-664">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="05098-665">Aby zademonstrować podejście DTO, zaktualizuj `TodoItem` klasę w celu uwzględnienia pola tajnego:</span><span class="sxs-lookup"><span data-stu-id="05098-665">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="05098-666">Pole tajne musi być ukryte w tej aplikacji, ale aplikacja administracyjna mogła ją uwidocznić.</span><span class="sxs-lookup"><span data-stu-id="05098-666">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="05098-667">Sprawdź, czy można opublikować i pobrać pole tajne.</span><span class="sxs-lookup"><span data-stu-id="05098-667">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="05098-668">Utwórz model DTO:</span><span class="sxs-lookup"><span data-stu-id="05098-668">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="05098-669">Zaktualizuj, `TodoItemsController` Aby użyć `TodoItemDTO` :</span><span class="sxs-lookup"><span data-stu-id="05098-669">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="05098-670">Upewnij się, że nie można opublikować lub pobrać pola tajnego.</span><span class="sxs-lookup"><span data-stu-id="05098-670">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="05098-671">Wywoływanie interfejsu API sieci Web przy użyciu języka JavaScript</span><span class="sxs-lookup"><span data-stu-id="05098-671">Call the web API with JavaScript</span></span>

<span data-ttu-id="05098-672">Zobacz [Samouczek: wywoływanie interfejsu API sieci web ASP.NET Core przy użyciu języka JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="05098-672">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="05098-673">Z tego samouczka dowiesz się, jak wykonywać następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="05098-673">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="05098-674">Utwórz projekt interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="05098-674">Create a web API project.</span></span>
> * <span data-ttu-id="05098-675">Dodaj klasę modelu i kontekst bazy danych.</span><span class="sxs-lookup"><span data-stu-id="05098-675">Add a model class and a database context.</span></span>
> * <span data-ttu-id="05098-676">Dodaj kontroler.</span><span class="sxs-lookup"><span data-stu-id="05098-676">Add a controller.</span></span>
> * <span data-ttu-id="05098-677">Dodaj metody CRUD.</span><span class="sxs-lookup"><span data-stu-id="05098-677">Add CRUD methods.</span></span>
> * <span data-ttu-id="05098-678">Skonfiguruj ścieżki routingu i adresów URL.</span><span class="sxs-lookup"><span data-stu-id="05098-678">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="05098-679">Określ wartości zwracane.</span><span class="sxs-lookup"><span data-stu-id="05098-679">Specify return values.</span></span>
> * <span data-ttu-id="05098-680">Wywołaj interfejs API sieci Web za pomocą programu Poster.</span><span class="sxs-lookup"><span data-stu-id="05098-680">Call the web API with Postman.</span></span>
> * <span data-ttu-id="05098-681">Wywołaj interfejs API sieci Web za pomocą języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="05098-681">Call the web API with JavaScript.</span></span>

<span data-ttu-id="05098-682">Na końcu znajduje się internetowy interfejs API, który może zarządzać elementami do wykonania przechowywanymi w relacyjnej bazie danych.</span><span class="sxs-lookup"><span data-stu-id="05098-682">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview-21"></a><span data-ttu-id="05098-683">Przegląd 2,1</span><span class="sxs-lookup"><span data-stu-id="05098-683">Overview 2.1</span></span>

<span data-ttu-id="05098-684">Ten samouczek tworzy następujący interfejs API:</span><span class="sxs-lookup"><span data-stu-id="05098-684">This tutorial creates the following API:</span></span>

|<span data-ttu-id="05098-685">Interfejs API</span><span class="sxs-lookup"><span data-stu-id="05098-685">API</span></span> | <span data-ttu-id="05098-686">Opis</span><span class="sxs-lookup"><span data-stu-id="05098-686">Description</span></span> | <span data-ttu-id="05098-687">Treść żądania</span><span class="sxs-lookup"><span data-stu-id="05098-687">Request body</span></span> | <span data-ttu-id="05098-688">Treść odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="05098-688">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="05098-689">Pobierz/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="05098-689">GET /api/TodoItems</span></span> | <span data-ttu-id="05098-690">Pobierz wszystkie elementy do wykonania</span><span class="sxs-lookup"><span data-stu-id="05098-690">Get all to-do items</span></span> | <span data-ttu-id="05098-691">Brak</span><span class="sxs-lookup"><span data-stu-id="05098-691">None</span></span> | <span data-ttu-id="05098-692">Tablica elementów do wykonania</span><span class="sxs-lookup"><span data-stu-id="05098-692">Array of to-do items</span></span>|
|<span data-ttu-id="05098-693">Pobierz/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="05098-693">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="05098-694">Pobieranie elementu według identyfikatora</span><span class="sxs-lookup"><span data-stu-id="05098-694">Get an item by ID</span></span> | <span data-ttu-id="05098-695">Brak</span><span class="sxs-lookup"><span data-stu-id="05098-695">None</span></span> | <span data-ttu-id="05098-696">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="05098-696">To-do item</span></span>|
|<span data-ttu-id="05098-697">Opublikuj/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="05098-697">POST /api/TodoItems</span></span> | <span data-ttu-id="05098-698">Dodaj nowy element</span><span class="sxs-lookup"><span data-stu-id="05098-698">Add a new item</span></span> | <span data-ttu-id="05098-699">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="05098-699">To-do item</span></span> | <span data-ttu-id="05098-700">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="05098-700">To-do item</span></span> |
|<span data-ttu-id="05098-701">Umieść/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="05098-701">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="05098-702">Aktualizowanie istniejącego elementu &nbsp;</span><span class="sxs-lookup"><span data-stu-id="05098-702">Update an existing item &nbsp;</span></span> | <span data-ttu-id="05098-703">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="05098-703">To-do item</span></span> | <span data-ttu-id="05098-704">Brak</span><span class="sxs-lookup"><span data-stu-id="05098-704">None</span></span> |
|<span data-ttu-id="05098-705">Usuń/api/TodoItems/{id} &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="05098-705">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="05098-706">Usuń element &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="05098-706">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="05098-707">Brak</span><span class="sxs-lookup"><span data-stu-id="05098-707">None</span></span> | <span data-ttu-id="05098-708">Brak</span><span class="sxs-lookup"><span data-stu-id="05098-708">None</span></span>|

<span data-ttu-id="05098-709">Na poniższym diagramie przedstawiono projekt aplikacji.</span><span class="sxs-lookup"><span data-stu-id="05098-709">The following diagram shows the design of the app.</span></span>

![Klient jest reprezentowany przez pole po lewej stronie.](first-web-api/_static/architecture.png)

## <a name="prerequisites-21"></a><span data-ttu-id="05098-715">Wymagania wstępne 2,1</span><span class="sxs-lookup"><span data-stu-id="05098-715">Prerequisites 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="05098-716">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="05098-716">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="05098-717">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="05098-717">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="05098-718">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="05098-718">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project-21"></a><span data-ttu-id="05098-719">Tworzenie projektu sieci Web 2,1</span><span class="sxs-lookup"><span data-stu-id="05098-719">Create a web project 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="05098-720">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="05098-720">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="05098-721">Z menu **plik** wybierz pozycję **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="05098-721">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="05098-722">Wybierz szablon **aplikacja sieci Web ASP.NET Core** a następnie kliknij przycisk **dalej**.</span><span class="sxs-lookup"><span data-stu-id="05098-722">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="05098-723">Nazwij projekt *TodoApi* i kliknij pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="05098-723">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="05098-724">W oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** upewnij się, że wybrano opcję **.net Core** i **ASP.NET Core 2,2** .</span><span class="sxs-lookup"><span data-stu-id="05098-724">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="05098-725">Wybierz szablon **interfejsu API** i kliknij przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="05098-725">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="05098-726">**Nie** zaznaczaj opcji **Włącz obsługę platformy Docker**.</span><span class="sxs-lookup"><span data-stu-id="05098-726">**Don't** select **Enable Docker Support**.</span></span>

![Okno dialogowe programu VS New Project](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="05098-728">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="05098-728">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="05098-729">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="05098-729">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="05098-730">Zmień katalog ( `cd` ) do folderu, który będzie zawierać folder projektu.</span><span class="sxs-lookup"><span data-stu-id="05098-730">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="05098-731">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="05098-731">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="05098-732">Te polecenia tworzą nowy projekt internetowego interfejsu API i otwierają nowe wystąpienie Visual Studio Code w nowym folderze projektu.</span><span class="sxs-lookup"><span data-stu-id="05098-732">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="05098-733">Gdy zostanie wyświetlone okno dialogowe z pytaniem, czy chcesz dodać wymagane zasoby do projektu, wybierz opcję **tak**.</span><span class="sxs-lookup"><span data-stu-id="05098-733">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="05098-734">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="05098-734">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="05098-735">Wybierz pozycję **plik** > **nowe rozwiązanie**.</span><span class="sxs-lookup"><span data-stu-id="05098-735">Select **File** > **New Solution**.</span></span>

  ![macOS nowe rozwiązanie](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="05098-737">W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz **.NET Core** pozycję  >  **App**  >  **interfejs API** aplikacji .NET Core  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="05098-737">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="05098-738">W wersji 8,6 lub nowszej wybierz pozycję **Web and Console**  >  **App**  >  **interfejs API**  >  **Next** aplikacji sieci Web i konsoli.</span><span class="sxs-lookup"><span data-stu-id="05098-738">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="05098-739">W oknie dialogowym **Konfigurowanie nowego interfejsu API sieci Web ASP.NET Core** wybierz najnowszą platformę **docelową** programu .NET Core 2. x.</span><span class="sxs-lookup"><span data-stu-id="05098-739">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="05098-740">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="05098-740">Select **Next**.</span></span>

* <span data-ttu-id="05098-741">Wprowadź *TodoApi* jako **nazwę projektu** , a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="05098-741">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![okno dialogowe konfiguracji](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api-21"></a><span data-ttu-id="05098-743">Testowanie interfejsu API 2,1</span><span class="sxs-lookup"><span data-stu-id="05098-743">Test the API 2.1</span></span>

<span data-ttu-id="05098-744">Szablon projektu tworzy `values` interfejs API.</span><span class="sxs-lookup"><span data-stu-id="05098-744">The project template creates a `values` API.</span></span> <span data-ttu-id="05098-745">Wywołaj `Get` metodę z przeglądarki, aby przetestować aplikację.</span><span class="sxs-lookup"><span data-stu-id="05098-745">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="05098-746">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="05098-746">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="05098-747">Naciśnij klawisze CTRL + F5, aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="05098-747">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="05098-748">Program Visual Studio uruchamia przeglądarkę i przechodzi do `https://localhost:<port>/api/values` lokalizacji, gdzie `<port>` jest losowo wybierany numer portu.</span><span class="sxs-lookup"><span data-stu-id="05098-748">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="05098-749">Jeśli zostanie wyświetlone okno dialogowe z pytaniem, czy należy zaufać certyfikatowi IIS Express, wybierz pozycję **tak**.</span><span class="sxs-lookup"><span data-stu-id="05098-749">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="05098-750">W wyświetlonym oknie dialogowym **ostrzeżenia o zabezpieczeniach** wybierz pozycję **tak**.</span><span class="sxs-lookup"><span data-stu-id="05098-750">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="05098-751">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="05098-751">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="05098-752">Naciśnij klawisze CTRL + F5, aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="05098-752">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="05098-753">W przeglądarce przejdź do następującego adresu URL: `https://localhost:5001/api/values` .</span><span class="sxs-lookup"><span data-stu-id="05098-753">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="05098-754">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="05098-754">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="05098-755">Wybierz pozycję **Uruchom**  >  **Rozpocznij debugowanie** , aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="05098-755">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="05098-756">Visual Studio dla komputerów Mac uruchamia przeglądarkę i nawiguje do `https://localhost:<port>` , gdzie `<port>` jest losowo wybranym numerem portu.</span><span class="sxs-lookup"><span data-stu-id="05098-756">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="05098-757">Zwracany jest błąd HTTP 404 (nie znaleziono).</span><span class="sxs-lookup"><span data-stu-id="05098-757">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="05098-758">Dołącz `/api/values` do adresu URL (Zmień adres URL na `https://localhost:<port>/api/values` ).</span><span class="sxs-lookup"><span data-stu-id="05098-758">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="05098-759">Zostanie zwrócony następujący kod JSON:</span><span class="sxs-lookup"><span data-stu-id="05098-759">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class-21"></a><span data-ttu-id="05098-760">Dodaj klasę modelu 2,1</span><span class="sxs-lookup"><span data-stu-id="05098-760">Add a model class 2.1</span></span>

<span data-ttu-id="05098-761">*Model* to zestaw klas, które reprezentują dane zarządzane przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="05098-761">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="05098-762">Model tej aplikacji jest pojedynczą `TodoItem` klasą.</span><span class="sxs-lookup"><span data-stu-id="05098-762">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="05098-763">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="05098-763">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="05098-764">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt.</span><span class="sxs-lookup"><span data-stu-id="05098-764">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="05098-765">Wybierz pozycję **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="05098-765">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="05098-766">Nazwij folder *Models* .</span><span class="sxs-lookup"><span data-stu-id="05098-766">Name the folder *Models*.</span></span>

* <span data-ttu-id="05098-767">Kliknij prawym przyciskiem myszy *Models* folder i wybierz polecenie **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="05098-767">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="05098-768">Nadaj klasie nazwę *TodoItem* i wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="05098-768">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="05098-769">Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="05098-769">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="05098-770">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="05098-770">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="05098-771">Dodaj folder o nazwie *Models* .</span><span class="sxs-lookup"><span data-stu-id="05098-771">Add a folder named *Models*.</span></span>

* <span data-ttu-id="05098-772">Dodaj `TodoItem` do *Models* folderu klasę o następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="05098-772">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="05098-773">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="05098-773">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="05098-774">Kliknij prawym przyciskiem myszy projekt.</span><span class="sxs-lookup"><span data-stu-id="05098-774">Right-click the project.</span></span> <span data-ttu-id="05098-775">Wybierz pozycję **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="05098-775">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="05098-776">Nazwij folder *Models* .</span><span class="sxs-lookup"><span data-stu-id="05098-776">Name the folder *Models*.</span></span>

  ![Nowy folder](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="05098-778">Kliknij prawym przyciskiem myszy *Models* folder, a następnie wybierz pozycję **Dodaj** > **nowy plik** > **ogólna** > **pusta Klasa**.</span><span class="sxs-lookup"><span data-stu-id="05098-778">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="05098-779">Nazwij klasę *TodoItem*, a następnie kliknij pozycję **New (nowy**).</span><span class="sxs-lookup"><span data-stu-id="05098-779">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="05098-780">Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="05098-780">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="05098-781">`Id`Właściwość działa jako unikatowy klucz w relacyjnej bazie danych.</span><span class="sxs-lookup"><span data-stu-id="05098-781">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="05098-782">Klasy modelu mogą przejść do dowolnego miejsca w projekcie, ale *Models* folder jest używany przez Konwencję.</span><span class="sxs-lookup"><span data-stu-id="05098-782">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context-21"></a><span data-ttu-id="05098-783">Dodawanie kontekstu bazy danych 2,1</span><span class="sxs-lookup"><span data-stu-id="05098-783">Add a database context 2.1</span></span>

<span data-ttu-id="05098-784">*Kontekst bazy danych* jest główną klasą, która koordynuje Entity Framework funkcji dla modelu danych.</span><span class="sxs-lookup"><span data-stu-id="05098-784">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="05098-785">Ta klasa jest tworzona przez wyprowadzanie z `Microsoft.EntityFrameworkCore.DbContext` klasy.</span><span class="sxs-lookup"><span data-stu-id="05098-785">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="05098-786">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="05098-786">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="05098-787">Kliknij prawym przyciskiem myszy *Models* folder i wybierz polecenie **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="05098-787">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="05098-788">Nadaj klasie nazwę *TodoContext* i kliknij przycisk **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="05098-788">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="05098-789">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="05098-789">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="05098-790">Dodaj `TodoContext` klasę do *Models* folderu.</span><span class="sxs-lookup"><span data-stu-id="05098-790">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="05098-791">Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="05098-791">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context-21"></a><span data-ttu-id="05098-792">Rejestrowanie kontekstu bazy danych 2,1</span><span class="sxs-lookup"><span data-stu-id="05098-792">Register the database context 2.1</span></span>

<span data-ttu-id="05098-793">W ASP.NET Core usługi, takie jak kontekst bazy danych, muszą być zarejestrowane z kontenerem [iniekcji zależności (di)](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="05098-793">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="05098-794">Kontener udostępnia usługę kontrolerom.</span><span class="sxs-lookup"><span data-stu-id="05098-794">The container provides the service to controllers.</span></span>

<span data-ttu-id="05098-795">Zaktualizuj *Startup.cs* o następujący wyróżniony kod:</span><span class="sxs-lookup"><span data-stu-id="05098-795">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="05098-796">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="05098-796">The preceding code:</span></span>

* <span data-ttu-id="05098-797">Usuwa nieużywane `using` deklaracje.</span><span class="sxs-lookup"><span data-stu-id="05098-797">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="05098-798">Dodaje kontekst bazy danych do kontenera DI.</span><span class="sxs-lookup"><span data-stu-id="05098-798">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="05098-799">Określa, że kontekst bazy danych będzie używać bazy danych w pamięci.</span><span class="sxs-lookup"><span data-stu-id="05098-799">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller-21"></a><span data-ttu-id="05098-800">Dodawanie kontrolera 2,1</span><span class="sxs-lookup"><span data-stu-id="05098-800">Add a controller 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="05098-801">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="05098-801">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="05098-802">Kliknij prawym przyciskiem myszy folder *controllers* .</span><span class="sxs-lookup"><span data-stu-id="05098-802">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="05098-803">Wybierz pozycję **Dodaj** > **nowy element**.</span><span class="sxs-lookup"><span data-stu-id="05098-803">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="05098-804">W oknie dialogowym **Dodaj nowy element** wybierz szablon **Klasa kontrolera interfejsu API** .</span><span class="sxs-lookup"><span data-stu-id="05098-804">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="05098-805">Nadaj klasie nazwę *TodoController* i wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="05098-805">Name the class *TodoController*, and select **Add**.</span></span>

  ![Okno dialogowe Dodawanie nowego elementu z kontrolerem w polu wyszukiwania i wybranym kontrolerem interfejsu API sieci Web](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="05098-807">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="05098-807">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="05098-808">W folderze *controllers* Utwórz klasę o nazwie `TodoController` .</span><span class="sxs-lookup"><span data-stu-id="05098-808">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="05098-809">Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="05098-809">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="05098-810">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="05098-810">The preceding code:</span></span>

* <span data-ttu-id="05098-811">Definiuje klasę kontrolera interfejsu API bez metod.</span><span class="sxs-lookup"><span data-stu-id="05098-811">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="05098-812">Oznacza klasę [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atrybutem.</span><span class="sxs-lookup"><span data-stu-id="05098-812">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="05098-813">Ten atrybut wskazuje, że kontroler odpowiada na żądania interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="05098-813">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="05098-814">Aby uzyskać informacje o określonych zachowaniach, które włącza atrybut, zobacz <xref:web-api/index> .</span><span class="sxs-lookup"><span data-stu-id="05098-814">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="05098-815">Używa funkcji DI do iniekcji kontekstu bazy danych ( `TodoContext` ) do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="05098-815">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="05098-816">Kontekst bazy danych jest używany w każdej z metod [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) w kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="05098-816">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="05098-817">Dodaje element o nazwie `Item1` do bazy danych, jeśli baza danych jest pusta.</span><span class="sxs-lookup"><span data-stu-id="05098-817">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="05098-818">Ten kod znajduje się w konstruktorze, więc jest uruchamiany za każdym razem, gdy istnieje nowe żądanie HTTP.</span><span class="sxs-lookup"><span data-stu-id="05098-818">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="05098-819">Jeśli usuniesz wszystkie elementy, Konstruktor zostanie ponownie utworzony `Item1` przy następnym wywołaniu metody interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="05098-819">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="05098-820">Dlatego może wyglądać podobnie jak usunięcie nie zadziałało, gdy rzeczywiście zadziałało.</span><span class="sxs-lookup"><span data-stu-id="05098-820">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods-21"></a><span data-ttu-id="05098-821">Dodaj metody get 2,1</span><span class="sxs-lookup"><span data-stu-id="05098-821">Add Get methods 2.1</span></span>

<span data-ttu-id="05098-822">Aby udostępnić interfejs API, który pobiera elementy do wykonania, Dodaj następujące metody do `TodoController` klasy:</span><span class="sxs-lookup"><span data-stu-id="05098-822">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="05098-823">Te metody implementują dwa punkty końcowe GET:</span><span class="sxs-lookup"><span data-stu-id="05098-823">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="05098-824">Zatrzymaj aplikację, jeśli jest nadal uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="05098-824">Stop the app if it's still running.</span></span> <span data-ttu-id="05098-825">Następnie uruchom ją ponownie, aby uwzględnić najnowsze zmiany.</span><span class="sxs-lookup"><span data-stu-id="05098-825">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="05098-826">Przetestuj aplikację, wywołując dwa punkty końcowe z przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="05098-826">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="05098-827">Przykład:</span><span class="sxs-lookup"><span data-stu-id="05098-827">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="05098-828">Następująca odpowiedź HTTP jest generowana przez wywołanie `GetTodoItems` :</span><span class="sxs-lookup"><span data-stu-id="05098-828">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths-21"></a><span data-ttu-id="05098-829">Ścieżki routingu i adresu URL 2,1</span><span class="sxs-lookup"><span data-stu-id="05098-829">Routing and URL paths 2.1</span></span>

<span data-ttu-id="05098-830">Ten [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) atrybut oznacza metodę, która reaguje na żądanie HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="05098-830">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="05098-831">Ścieżka adresu URL dla każdej metody jest zbudowana w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="05098-831">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="05098-832">Zacznij od ciągu szablonu w `Route` atrybucie kontrolera:</span><span class="sxs-lookup"><span data-stu-id="05098-832">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="05098-833">Zastąp `[controller]` nazwą kontrolera, którą Konwencją jest nazwa klasy kontrolera minus sufiks "Controller".</span><span class="sxs-lookup"><span data-stu-id="05098-833">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="05098-834">W przypadku tego przykładu nazwa klasy kontrolera to kontroler do **zrobienia**, więc nazwa kontrolera to "do zrobienia".</span><span class="sxs-lookup"><span data-stu-id="05098-834">For this sample, the controller class name is **Todo** Controller, so the controller name is "todo".</span></span> <span data-ttu-id="05098-835">W ASP.NET Core [routingu](xref:mvc/controllers/routing) jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="05098-835">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="05098-836">Jeśli `[HttpGet]` atrybut ma szablon trasy (na przykład `[HttpGet("products")]` ), Dodaj go do ścieżki.</span><span class="sxs-lookup"><span data-stu-id="05098-836">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="05098-837">Ten przykład nie używa szablonu.</span><span class="sxs-lookup"><span data-stu-id="05098-837">This sample doesn't use a template.</span></span> <span data-ttu-id="05098-838">Aby uzyskać więcej informacji, zobacz temat [Routing atrybutów z atrybutami http [Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="05098-838">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="05098-839">W poniższej `GetTodoItem` metodzie `"{id}"` jest zmienną zastępczą dla unikatowego identyfikatora elementu do wykonania.</span><span class="sxs-lookup"><span data-stu-id="05098-839">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="05098-840">Gdy `GetTodoItem` jest wywoływana, wartość `"{id}"` w adresie URL jest podawana do metody w jej `id` parametrze.</span><span class="sxs-lookup"><span data-stu-id="05098-840">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values-21"></a><span data-ttu-id="05098-841">Wartości zwracane 2,1</span><span class="sxs-lookup"><span data-stu-id="05098-841">Return values 2.1</span></span>

<span data-ttu-id="05098-842">Zwracany typ `GetTodoItems` `GetTodoItem` metod i jest [ \<T> typem ActionResult](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="05098-842">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="05098-843">ASP.NET Core automatycznie serializować obiektu do [formatu JSON](https://www.json.org/) i zapisuje kod JSON w treści komunikatu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="05098-843">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="05098-844">Kod odpowiedzi dla tego typu zwracanego to 200, przy założeniu, że nie istnieją Nieobsłużone wyjątki.</span><span class="sxs-lookup"><span data-stu-id="05098-844">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="05098-845">Nieobsłużone wyjątki są tłumaczone na błędy 5xx.</span><span class="sxs-lookup"><span data-stu-id="05098-845">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="05098-846">`ActionResult` typy zwracane mogą reprezentować szeroką gamę kodów stanu HTTP.</span><span class="sxs-lookup"><span data-stu-id="05098-846">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="05098-847">Na przykład `GetTodoItem` może zwracać dwie różne wartości stanu:</span><span class="sxs-lookup"><span data-stu-id="05098-847">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="05098-848">Jeśli żaden element nie jest zgodny z żądanym IDENTYFIKATORem, metoda zwróci <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> Kod błędu 404.</span><span class="sxs-lookup"><span data-stu-id="05098-848">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="05098-849">W przeciwnym razie metoda zwraca 200 z treścią odpowiedzi JSON.</span><span class="sxs-lookup"><span data-stu-id="05098-849">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="05098-850">Zwracanie `item` wyników w odpowiedzi HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="05098-850">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method-21"></a><span data-ttu-id="05098-851">Testowanie metody GetTodoItems 2,1</span><span class="sxs-lookup"><span data-stu-id="05098-851">Test the GetTodoItems method 2.1</span></span>

<span data-ttu-id="05098-852">Ten samouczek używa programu do testowania interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="05098-852">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="05098-853">Zainstaluj program [Poster](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="05098-853">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="05098-854">Uruchom aplikację internetową.</span><span class="sxs-lookup"><span data-stu-id="05098-854">Start the web app.</span></span>
* <span data-ttu-id="05098-855">Uruchom wpis.</span><span class="sxs-lookup"><span data-stu-id="05098-855">Start Postman.</span></span>
* <span data-ttu-id="05098-856">Wyłącz **weryfikację certyfikatu SSL**.</span><span class="sxs-lookup"><span data-stu-id="05098-856">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="05098-857">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="05098-857">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="05098-858">W **File** obszarze > **Ustawienia** pliku (karta **Ogólne** ) Wyłącz **weryfikację certyfikatu SSL**.</span><span class="sxs-lookup"><span data-stu-id="05098-858">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="05098-859">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="05098-859">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="05098-860">Z poziomu preferencji programu **Poster**  >  **Preferences** (karta **Ogólne** ) Wyłącz **weryfikację certyfikatu SSL**.</span><span class="sxs-lookup"><span data-stu-id="05098-860">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="05098-861">Alternatywnie wybierz klucz i wybierz pozycję **Ustawienia**, a następnie wyłącz weryfikację certyfikatu SSL.</span><span class="sxs-lookup"><span data-stu-id="05098-861">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="05098-862">Po przetestowaniu kontrolera ponownie Włącz weryfikację certyfikatu SSL.</span><span class="sxs-lookup"><span data-stu-id="05098-862">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="05098-863">Utwórz nowe żądanie.</span><span class="sxs-lookup"><span data-stu-id="05098-863">Create a new request.</span></span>
  * <span data-ttu-id="05098-864">Ustaw metodę HTTP, aby **uzyskać**.</span><span class="sxs-lookup"><span data-stu-id="05098-864">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="05098-865">Ustaw identyfikator URI żądania na `https://localhost:<port>/api/todo` .</span><span class="sxs-lookup"><span data-stu-id="05098-865">Set the request URI to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="05098-866">Na przykład `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="05098-866">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="05098-867">Ustaw **dwa widoki okienka** w programie Poster.</span><span class="sxs-lookup"><span data-stu-id="05098-867">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="05098-868">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="05098-868">Select **Send**.</span></span>

![Ogłoś przy użyciu żądania GET](first-web-api/_static/2pv.png)

## <a name="add-a-create-method-21"></a><span data-ttu-id="05098-870">Dodawanie metody Create 2,1</span><span class="sxs-lookup"><span data-stu-id="05098-870">Add a Create method 2.1</span></span>

<span data-ttu-id="05098-871">Dodaj następującą `PostTodoItem` metodę w obszarze *controllers/TodoController. cs*:</span><span class="sxs-lookup"><span data-stu-id="05098-871">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="05098-872">Poprzedni kod jest metodą POST protokołu HTTP, jak wskazano w [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) atrybucie.</span><span class="sxs-lookup"><span data-stu-id="05098-872">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="05098-873">Metoda pobiera wartość elementu do wykonania z treści żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="05098-873">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="05098-874">`CreatedAtAction`Metoda:</span><span class="sxs-lookup"><span data-stu-id="05098-874">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="05098-875">Zwraca kod stanu HTTP 201, jeśli powodzenie.</span><span class="sxs-lookup"><span data-stu-id="05098-875">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="05098-876">HTTP 201 to standardowa odpowiedź dla metody POST protokołu HTTP, która tworzy nowy zasób na serwerze.</span><span class="sxs-lookup"><span data-stu-id="05098-876">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="05098-877">Dodaje `Location` nagłówek do odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="05098-877">Adds a `Location` header to the response.</span></span> <span data-ttu-id="05098-878">`Location`Nagłówek określa identyfikator URI nowo utworzonego elementu do wykonania.</span><span class="sxs-lookup"><span data-stu-id="05098-878">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="05098-879">Aby uzyskać więcej informacji, zobacz [10.2.2 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="05098-879">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="05098-880">Odwołuje się do `GetTodoItem` akcji tworzenia `Location` identyfikatora URI nagłówka.</span><span class="sxs-lookup"><span data-stu-id="05098-880">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="05098-881">`nameof`Słowo kluczowe języka C# służy do zapobiegania twardemu kodowaniu nazwy akcji w `CreatedAtAction` wywołaniu.</span><span class="sxs-lookup"><span data-stu-id="05098-881">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method-21"></a><span data-ttu-id="05098-882">Testowanie metody PostTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="05098-882">Test the PostTodoItem method 2.1</span></span>

* <span data-ttu-id="05098-883">Skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="05098-883">Build the project.</span></span>
* <span data-ttu-id="05098-884">W programie Poster ustaw metodę HTTP na `POST` .</span><span class="sxs-lookup"><span data-stu-id="05098-884">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="05098-885">Ustaw identyfikator URI na `https://localhost:<port>/api/Todo` .</span><span class="sxs-lookup"><span data-stu-id="05098-885">Set the URI to `https://localhost:<port>/api/Todo`.</span></span> <span data-ttu-id="05098-886">Na przykład `https://localhost:5001/api/Todo`.</span><span class="sxs-lookup"><span data-stu-id="05098-886">For example, `https://localhost:5001/api/Todo`.</span></span>
* <span data-ttu-id="05098-887">Wybierz kartę **Treść**.</span><span class="sxs-lookup"><span data-stu-id="05098-887">Select the **Body** tab.</span></span>
* <span data-ttu-id="05098-888">Wybierz przycisk radiowy **RAW** .</span><span class="sxs-lookup"><span data-stu-id="05098-888">Select the **raw** radio button.</span></span>
* <span data-ttu-id="05098-889">Ustaw typ na **JSON (Application/JSON)**.</span><span class="sxs-lookup"><span data-stu-id="05098-889">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="05098-890">W treści żądania wprowadź kod JSON dla elementu do wykonania:</span><span class="sxs-lookup"><span data-stu-id="05098-890">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="05098-891">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="05098-891">Select **Send**.</span></span>

  ![Ogłoś przy użyciu żądania Create](first-web-api/_static/create.png)

  <span data-ttu-id="05098-893">Jeśli wystąpi błąd 405 metody niedozwolonej, jest to prawdopodobnie wynik niekompilowania projektu po dodaniu `PostTodoItem` metody.</span><span class="sxs-lookup"><span data-stu-id="05098-893">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri-21"></a><span data-ttu-id="05098-894">Testowanie identyfikatora URI nagłówka lokalizacji 2,1</span><span class="sxs-lookup"><span data-stu-id="05098-894">Test the location header URI 2.1</span></span>

* <span data-ttu-id="05098-895">Wybierz kartę **nagłówki** w okienku **odpowiedź** .</span><span class="sxs-lookup"><span data-stu-id="05098-895">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="05098-896">Skopiuj wartość nagłówka **lokalizacji** :</span><span class="sxs-lookup"><span data-stu-id="05098-896">Copy the **Location** header value:</span></span>

  ![Karta nagłówki w konsoli programu Poster](first-web-api/_static/pmc2.png)

* <span data-ttu-id="05098-898">Ustaw metodę, aby uzyskać.</span><span class="sxs-lookup"><span data-stu-id="05098-898">Set the method to GET.</span></span>
* <span data-ttu-id="05098-899">Ustaw identyfikator URI na `https://localhost:<port>/api/TodoItems/2` .</span><span class="sxs-lookup"><span data-stu-id="05098-899">Set the URI to `https://localhost:<port>/api/TodoItems/2`.</span></span> <span data-ttu-id="05098-900">Na przykład `https://localhost:5001/api/TodoItems/2`.</span><span class="sxs-lookup"><span data-stu-id="05098-900">For example, `https://localhost:5001/api/TodoItems/2`.</span></span>
* <span data-ttu-id="05098-901">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="05098-901">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method-21"></a><span data-ttu-id="05098-902">Dodawanie metody PutTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="05098-902">Add a PutTodoItem method 2.1</span></span>

<span data-ttu-id="05098-903">Dodaj następującą `PutTodoItem` metodę:</span><span class="sxs-lookup"><span data-stu-id="05098-903">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="05098-904">`PutTodoItem` jest podobny do `PostTodoItem` , z tą różnicą, że używa protokołu HTTP Put.</span><span class="sxs-lookup"><span data-stu-id="05098-904">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="05098-905">Odpowiedź to [204 (brak zawartości)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="05098-905">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="05098-906">Zgodnie ze specyfikacją protokołu HTTP żądanie PUT wymaga, aby klient wysłał całą zaktualizowaną jednostkę, a nie tylko te zmiany.</span><span class="sxs-lookup"><span data-stu-id="05098-906">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="05098-907">Aby zapewnić obsługę częściowych aktualizacji, użyj [poprawki http](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="05098-907">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="05098-908">Jeśli wystąpi błąd podczas wywoływania `PutTodoItem` , wywołaj, `GET` Aby upewnić się, że w bazie danych znajduje się element.</span><span class="sxs-lookup"><span data-stu-id="05098-908">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method-21"></a><span data-ttu-id="05098-909">Testowanie metody PutTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="05098-909">Test the PutTodoItem method 2.1</span></span>

<span data-ttu-id="05098-910">Ten przykład korzysta z bazy danych w pamięci, która musi zostać zainicjowana za każdym razem, gdy aplikacja zostanie uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="05098-910">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="05098-911">Przed wykonaniem wywołania PUT musi istnieć element w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="05098-911">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="05098-912">Przed wykonaniem wywołania PUT należy wywołać metodę GET, aby upewnić się, że istnieje element w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="05098-912">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="05098-913">Zaktualizuj element do wykonania o identyfikatorze 1 i ustaw jego nazwę na "Źródło danych":</span><span class="sxs-lookup"><span data-stu-id="05098-913">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="05098-914">Na poniższej ilustracji przedstawiono aktualizację programu Poster:</span><span class="sxs-lookup"><span data-stu-id="05098-914">The following image shows the Postman update:</span></span>

![Konsola programu Poster pokazująca odpowiedź 204 (brak zawartości)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method-21"></a><span data-ttu-id="05098-916">Dodawanie metody DeleteTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="05098-916">Add a DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="05098-917">Dodaj następującą `DeleteTodoItem` metodę:</span><span class="sxs-lookup"><span data-stu-id="05098-917">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="05098-918">`DeleteTodoItem`Odpowiedź to [204 (brak zawartości)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="05098-918">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method-21"></a><span data-ttu-id="05098-919">Testowanie metody DeleteTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="05098-919">Test the DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="05098-920">Użyj programu Poster, aby usunąć element do wykonania:</span><span class="sxs-lookup"><span data-stu-id="05098-920">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="05098-921">Ustaw metodę na `DELETE` .</span><span class="sxs-lookup"><span data-stu-id="05098-921">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="05098-922">Ustaw identyfikator URI obiektu do usunięcia (na przykład `https://localhost:5001/api/todo/1` ).</span><span class="sxs-lookup"><span data-stu-id="05098-922">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="05098-923">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="05098-923">Select **Send**.</span></span>

<span data-ttu-id="05098-924">Przykładowa aplikacja umożliwia usunięcie wszystkich elementów.</span><span class="sxs-lookup"><span data-stu-id="05098-924">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="05098-925">Jednak po usunięciu ostatniego elementu jest on tworzony przez konstruktora klasy modelu przy następnym wywołaniu interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="05098-925">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript-21"></a><span data-ttu-id="05098-926">Wywoływanie interfejsu API sieci Web przy użyciu języka JavaScript 2,1</span><span class="sxs-lookup"><span data-stu-id="05098-926">Call the web API with JavaScript 2.1</span></span>

<span data-ttu-id="05098-927">W tej sekcji zostanie dodana strona HTML, która używa języka JavaScript do wywoływania internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="05098-927">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="05098-928">jQuery inicjuje żądanie.</span><span class="sxs-lookup"><span data-stu-id="05098-928">jQuery initiates the request.</span></span> <span data-ttu-id="05098-929">Język JavaScript aktualizuje stronę ze szczegółowymi informacjami z odpowiedzi internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="05098-929">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="05098-930">Skonfiguruj aplikację do [obsługi plików statycznych](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) i [Włącz domyślne mapowanie plików](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) , aktualizując *Startup.cs* z następującym wyróżnionym kodem:</span><span class="sxs-lookup"><span data-stu-id="05098-930">Configure the app to [serve static files](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) and [enable default file mapping](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="05098-931">Utwórz folder *wwwroot* w katalogu projektu.</span><span class="sxs-lookup"><span data-stu-id="05098-931">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="05098-932">Dodaj plik HTML o nazwie *index.html* do katalogu *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="05098-932">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="05098-933">Zastąp jego zawartość następującym znacznikiem:</span><span class="sxs-lookup"><span data-stu-id="05098-933">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="05098-934">Dodaj plik języka JavaScript o nazwie *site.js* do katalogu *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="05098-934">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="05098-935">Zastąp zawartość poniższym kodem:</span><span class="sxs-lookup"><span data-stu-id="05098-935">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="05098-936">Zmiana ustawień uruchamiania projektu ASP.NET Core może być wymagana do lokalnego przetestowania strony HTML:</span><span class="sxs-lookup"><span data-stu-id="05098-936">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="05098-937">Otwórz *Properties\launchSettings.jsna*.</span><span class="sxs-lookup"><span data-stu-id="05098-937">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="05098-938">Usuń `launchUrl` Właściwość, aby wymusić otwieranie przez aplikację w *index.html* &mdash; domyślnego pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="05098-938">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="05098-939">Ten przykład wywołuje wszystkie metody CRUD internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="05098-939">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="05098-940">Poniżej znajdują się wyjaśnienia wywołań interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="05098-940">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items-21"></a><span data-ttu-id="05098-941">Pobierz listę elementów do wykonania 2,1</span><span class="sxs-lookup"><span data-stu-id="05098-941">Get a list of to-do items 2.1</span></span>

<span data-ttu-id="05098-942">jQuery wysyła żądanie HTTP GET do internetowego interfejsu API, który zwraca kod JSON reprezentujący tablicę elementów do wykonania.</span><span class="sxs-lookup"><span data-stu-id="05098-942">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="05098-943">`success`Funkcja wywołania zwrotnego jest wywoływana, jeśli żądanie zakończy się pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="05098-943">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="05098-944">W wywołaniu zwrotnym model DOM jest aktualizowany przy użyciu informacji o tym do wykonania.</span><span class="sxs-lookup"><span data-stu-id="05098-944">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item-21"></a><span data-ttu-id="05098-945">Dodaj element do wykonania 2,1</span><span class="sxs-lookup"><span data-stu-id="05098-945">Add a to-do item 2.1</span></span>

<span data-ttu-id="05098-946">jQuery wysyła żądanie HTTP POST z elementem do wykonania w treści żądania.</span><span class="sxs-lookup"><span data-stu-id="05098-946">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="05098-947">`accepts`Opcje i `contentType` są ustawione na, aby `application/json` określić typ nośnika odbierany i wysyłany.</span><span class="sxs-lookup"><span data-stu-id="05098-947">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="05098-948">Element do wykonania jest konwertowany na format JSON przy użyciu [formatu JSON. stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="05098-948">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="05098-949">Gdy interfejs API zwraca kod stanu pomyślnego, `getData` Funkcja jest wywoływana w celu zaktualizowania tabeli HTML.</span><span class="sxs-lookup"><span data-stu-id="05098-949">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item-21"></a><span data-ttu-id="05098-950">Aktualizowanie elementu do wykonania 2,1</span><span class="sxs-lookup"><span data-stu-id="05098-950">Update a to-do item 2.1</span></span>

<span data-ttu-id="05098-951">Aktualizowanie elementu do wykonania jest podobne do dodawania jednego.</span><span class="sxs-lookup"><span data-stu-id="05098-951">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="05098-952">`url`Zmiany mające na celu dodanie unikatowego identyfikatora elementu i `type` is `PUT` .</span><span class="sxs-lookup"><span data-stu-id="05098-952">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item-21"></a><span data-ttu-id="05098-953">Usuń element do wykonania 2,1</span><span class="sxs-lookup"><span data-stu-id="05098-953">Delete a to-do item 2.1</span></span>

<span data-ttu-id="05098-954">Usuwanie elementu do wykonania jest realizowane przez ustawienie `type` w WYWOŁANIU AJAX `DELETE` i określenie unikatowego identyfikatora elementu w adresie URL.</span><span class="sxs-lookup"><span data-stu-id="05098-954">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api-21"></a><span data-ttu-id="05098-955">Dodawanie obsługi uwierzytelniania do internetowego interfejsu API 2,1</span><span class="sxs-lookup"><span data-stu-id="05098-955">Add authentication support to a web API 2.1</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources-21"></a><span data-ttu-id="05098-956">Dodatkowe zasoby 2,1</span><span class="sxs-lookup"><span data-stu-id="05098-956">Additional resources 2.1</span></span>

<span data-ttu-id="05098-957">[Wyświetl lub Pobierz przykładowy kod dla tego samouczka](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="05098-957">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="05098-958">Zobacz artykuł [jak pobrać](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="05098-958">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="05098-959">Więcej informacji można znaleźć w następujących zasobach:</span><span class="sxs-lookup"><span data-stu-id="05098-959">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="05098-960">Wersja tego samouczka usługi YouTube</span><span class="sxs-lookup"><span data-stu-id="05098-960">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
