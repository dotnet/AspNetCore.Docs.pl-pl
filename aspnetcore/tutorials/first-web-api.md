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
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "96175055"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="067dc-103">Samouczek: Tworzenie internetowego interfejsu API za pomocą ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="067dc-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="067dc-104">Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirka Larkin](https://twitter.com/serpent5)i [Jan Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="067dc-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="067dc-105">Ten samouczek uczy się podstaw tworzenia interfejsu API sieci Web za pomocą ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="067dc-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="067dc-106">Ten samouczek zawiera informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="067dc-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="067dc-107">Utwórz projekt interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="067dc-107">Create a web API project.</span></span>
> * <span data-ttu-id="067dc-108">Dodaj klasę modelu i kontekst bazy danych.</span><span class="sxs-lookup"><span data-stu-id="067dc-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="067dc-109">Tworzy szkielet kontrolera z metodami CRUD.</span><span class="sxs-lookup"><span data-stu-id="067dc-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="067dc-110">Skonfiguruj Routing, ścieżki URL i wartości zwracane.</span><span class="sxs-lookup"><span data-stu-id="067dc-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="067dc-111">Wywołaj interfejs API sieci Web za pomocą programu Poster.</span><span class="sxs-lookup"><span data-stu-id="067dc-111">Call the web API with Postman.</span></span>

<span data-ttu-id="067dc-112">Na końcu znajduje się internetowy interfejs API, który może zarządzać elementami do wykonania przechowywanymi w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="067dc-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="067dc-113">Omówienie</span><span class="sxs-lookup"><span data-stu-id="067dc-113">Overview</span></span>

<span data-ttu-id="067dc-114">Ten samouczek tworzy następujący interfejs API:</span><span class="sxs-lookup"><span data-stu-id="067dc-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="067dc-115">Interfejs API</span><span class="sxs-lookup"><span data-stu-id="067dc-115">API</span></span> | <span data-ttu-id="067dc-116">Opis</span><span class="sxs-lookup"><span data-stu-id="067dc-116">Description</span></span> | <span data-ttu-id="067dc-117">Treść żądania</span><span class="sxs-lookup"><span data-stu-id="067dc-117">Request body</span></span> | <span data-ttu-id="067dc-118">Treść odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="067dc-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="067dc-119">Pobierz wszystkie elementy do wykonania</span><span class="sxs-lookup"><span data-stu-id="067dc-119">Get all to-do items</span></span> | <span data-ttu-id="067dc-120">Brak</span><span class="sxs-lookup"><span data-stu-id="067dc-120">None</span></span> | <span data-ttu-id="067dc-121">Tablica elementów do wykonania</span><span class="sxs-lookup"><span data-stu-id="067dc-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="067dc-122">Pobieranie elementu według identyfikatora</span><span class="sxs-lookup"><span data-stu-id="067dc-122">Get an item by ID</span></span> | <span data-ttu-id="067dc-123">Brak</span><span class="sxs-lookup"><span data-stu-id="067dc-123">None</span></span> | <span data-ttu-id="067dc-124">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="067dc-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="067dc-125">Dodaj nowy element</span><span class="sxs-lookup"><span data-stu-id="067dc-125">Add a new item</span></span> | <span data-ttu-id="067dc-126">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="067dc-126">To-do item</span></span> | <span data-ttu-id="067dc-127">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="067dc-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="067dc-128">Aktualizowanie istniejącego elementu &nbsp;</span><span class="sxs-lookup"><span data-stu-id="067dc-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="067dc-129">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="067dc-129">To-do item</span></span> | <span data-ttu-id="067dc-130">Brak</span><span class="sxs-lookup"><span data-stu-id="067dc-130">None</span></span> |
|<span data-ttu-id="067dc-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="067dc-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="067dc-132">Usuń element &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="067dc-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="067dc-133">Brak</span><span class="sxs-lookup"><span data-stu-id="067dc-133">None</span></span> | <span data-ttu-id="067dc-134">Brak</span><span class="sxs-lookup"><span data-stu-id="067dc-134">None</span></span>|

<span data-ttu-id="067dc-135">Na poniższym diagramie przedstawiono projekt aplikacji.</span><span class="sxs-lookup"><span data-stu-id="067dc-135">The following diagram shows the design of the app.</span></span>

![Klient jest reprezentowany przez pole po lewej stronie.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="067dc-141">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="067dc-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="067dc-142">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="067dc-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="067dc-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="067dc-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="067dc-144">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="067dc-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="067dc-145">Tworzenie projektu sieci Web</span><span class="sxs-lookup"><span data-stu-id="067dc-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="067dc-146">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="067dc-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="067dc-147">Z menu **plik** wybierz pozycję **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="067dc-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="067dc-148">Wybierz szablon **aplikacja sieci Web ASP.NET Core** a następnie kliknij przycisk **dalej**.</span><span class="sxs-lookup"><span data-stu-id="067dc-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="067dc-149">Nazwij projekt *TodoApi* i kliknij pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="067dc-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="067dc-150">W oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** upewnij się, że wybrano opcję **.net Core** i **ASP.NET Core 5,0** .</span><span class="sxs-lookup"><span data-stu-id="067dc-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 5.0** are selected.</span></span> <span data-ttu-id="067dc-151">Wybierz szablon **interfejsu API** i kliknij przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="067dc-151">Select the **API** template and click **Create**.</span></span>

![Okno dialogowe programu VS New Project](first-web-api/_static/5/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="067dc-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="067dc-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="067dc-154">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="067dc-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="067dc-155">Zmień katalog ( `cd` ) do folderu, który będzie zawierać folder projektu.</span><span class="sxs-lookup"><span data-stu-id="067dc-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="067dc-156">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="067dc-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="067dc-157">Gdy zostanie wyświetlone okno dialogowe z pytaniem, czy chcesz dodać wymagane zasoby do projektu, wybierz opcję **tak**.</span><span class="sxs-lookup"><span data-stu-id="067dc-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="067dc-158">Poprzednie polecenia:</span><span class="sxs-lookup"><span data-stu-id="067dc-158">The preceding commands:</span></span>

  * <span data-ttu-id="067dc-159">Tworzy nowy projekt internetowego interfejsu API i otwiera go w Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="067dc-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="067dc-160">Dodaje pakiety NuGet, które są wymagane w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="067dc-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="067dc-161">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="067dc-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="067dc-162">Wybierz pozycję **plik** > **nowe rozwiązanie**.</span><span class="sxs-lookup"><span data-stu-id="067dc-162">Select **File** > **New Solution**.</span></span>

  ![macOS nowe rozwiązanie](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="067dc-164">W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz pozycję  >    >  **interfejs API** aplikacji .NET Core  >  .</span><span class="sxs-lookup"><span data-stu-id="067dc-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="067dc-165">W wersji 8,6 lub nowszej wybierz pozycję   >    >  **interfejs API**  >  aplikacji sieci Web i konsoli.</span><span class="sxs-lookup"><span data-stu-id="067dc-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![Wybór szablonu interfejsu API macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="067dc-167">W oknie dialogowym **Konfigurowanie nowego interfejsu API sieci Web ASP.NET Core** wybierz najnowszą platformę **docelową**.NET Core 5. x.</span><span class="sxs-lookup"><span data-stu-id="067dc-167">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 5.x **Target Framework**.</span></span> <span data-ttu-id="067dc-168">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="067dc-168">Select **Next**.</span></span>

* <span data-ttu-id="067dc-169">Wprowadź *TodoApi* jako **nazwę projektu** , a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="067dc-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![okno dialogowe konfiguracji](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="067dc-171">Otwórz Terminal poleceń w folderze projektu i uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="067dc-171">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-project"></a><span data-ttu-id="067dc-172">Testowanie projektu</span><span class="sxs-lookup"><span data-stu-id="067dc-172">Test the project</span></span>

<span data-ttu-id="067dc-173">Szablon projektu tworzy `WeatherForecast` interfejs API z obsługą [struktury Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span><span class="sxs-lookup"><span data-stu-id="067dc-173">The project template creates a `WeatherForecast` API with support for [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="067dc-174">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="067dc-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="067dc-175">Naciśnij klawisze CTRL + F5, aby uruchomić bez debugera.</span><span class="sxs-lookup"><span data-stu-id="067dc-175">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="067dc-176">Zostanie uruchomiony program Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="067dc-176">Visual Studio launches:</span></span>

* <span data-ttu-id="067dc-177">Serwer sieci Web IIS Express.</span><span class="sxs-lookup"><span data-stu-id="067dc-177">The IIS Express web server.</span></span>
* <span data-ttu-id="067dc-178">Domyślna przeglądarka i nawiguje do `https://localhost:<port>/swagger/index.html` , gdzie `<port>` jest losowo wybranym numerem portu.</span><span class="sxs-lookup"><span data-stu-id="067dc-178">The default browser and navigates to `https://localhost:<port>/swagger/index.html`, where `<port>` is a randomly chosen port number.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="067dc-179">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="067dc-179">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/trustCertVSC.md)]

<span data-ttu-id="067dc-180">Naciśnij klawisze CTRL + F5, aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="067dc-180">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="067dc-181">W przeglądarce przejdź do następującego adresu URL: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span><span class="sxs-lookup"><span data-stu-id="067dc-181">In a browser, go to following URL: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="067dc-182">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="067dc-182">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="067dc-183">Wybierz pozycję **Uruchom**  >  **Rozpocznij debugowanie** , aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="067dc-183">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="067dc-184">Visual Studio dla komputerów Mac uruchamia przeglądarkę i nawiguje do `https://localhost:<port>` , gdzie `<port>` jest losowo wybranym numerem portu.</span><span class="sxs-lookup"><span data-stu-id="067dc-184">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="067dc-185">Zwracany jest błąd HTTP 404 (nie znaleziono).</span><span class="sxs-lookup"><span data-stu-id="067dc-185">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="067dc-186">Dołącz `/swagger` do adresu URL (Zmień adres URL na `https://localhost:<port>/swagger` ).</span><span class="sxs-lookup"><span data-stu-id="067dc-186">Append `/swagger` to the URL (change the URL to `https://localhost:<port>/swagger`).</span></span>

---

<span data-ttu-id="067dc-187">`/swagger/index.html`Zostanie wyświetlona strona Swagger.</span><span class="sxs-lookup"><span data-stu-id="067dc-187">The Swagger page `/swagger/index.html` is displayed.</span></span> <span data-ttu-id="067dc-188">Wybierz pozycję **Pobierz**  >  **spróbuj**  >  **wykonać** operację.</span><span class="sxs-lookup"><span data-stu-id="067dc-188">Select **GET** > **Try it out** > **Execute**.</span></span> <span data-ttu-id="067dc-189">Zostanie wyświetlona strona:</span><span class="sxs-lookup"><span data-stu-id="067dc-189">The page displays:</span></span>

* <span data-ttu-id="067dc-190">[Zwinięcie](https://curl.haxx.se/) polecenie do testowania interfejsu API WeatherForecast.</span><span class="sxs-lookup"><span data-stu-id="067dc-190">The [Curl](https://curl.haxx.se/) command to test the WeatherForecast API.</span></span>
* <span data-ttu-id="067dc-191">Adres URL służący do testowania interfejsu API WeatherForecast.</span><span class="sxs-lookup"><span data-stu-id="067dc-191">The URL to test the WeatherForecast API.</span></span>
* <span data-ttu-id="067dc-192">Kod odpowiedzi, treść i nagłówki.</span><span class="sxs-lookup"><span data-stu-id="067dc-192">The response code, body, and headers.</span></span>
* <span data-ttu-id="067dc-193">Pole listy rozwijanej z typami nośników oraz przykładową wartością i schematem.</span><span class="sxs-lookup"><span data-stu-id="067dc-193">A drop down list box with media types and the example value and schema.</span></span>

<!-- Review: Do we care the IE generates several errors. It shows the data, but with  Unrecognized response type; displaying content as text.
-->
<span data-ttu-id="067dc-194">Struktura Swagger służy do generowania przydatnej dokumentacji i stron pomocy dla interfejsów API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="067dc-194">Swagger is used to generate useful documentation and help pages for web APIs.</span></span> <span data-ttu-id="067dc-195">Ten samouczek koncentruje się na tworzeniu interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="067dc-195">This tutorial focuses on creating a web API.</span></span> <span data-ttu-id="067dc-196">Aby uzyskać więcej informacji na temat struktury Swagger, zobacz <xref:tutorials/web-api-help-pages-using-swagger> .</span><span class="sxs-lookup"><span data-stu-id="067dc-196">For more information on Swagger, see <xref:tutorials/web-api-help-pages-using-swagger>.</span></span>

<span data-ttu-id="067dc-197">Skopiuj i wklej **adres URL żądania** w przeglądarce:  `https://localhost:<port>/WeatherForecast`</span><span class="sxs-lookup"><span data-stu-id="067dc-197">Copy and paste the **Request URL** in the browser:  `https://localhost:<port>/WeatherForecast`</span></span>

<span data-ttu-id="067dc-198">Zwracany jest kod JSON podobny do następującego:</span><span class="sxs-lookup"><span data-stu-id="067dc-198">JSON similar to the following is returned:</span></span>

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

### <a name="update-the-launchurl"></a><span data-ttu-id="067dc-199">Aktualizowanie launchUrl</span><span class="sxs-lookup"><span data-stu-id="067dc-199">Update the launchUrl</span></span>

<span data-ttu-id="067dc-200">W *Properties\launchSettings.json* Update `launchUrl` od `"swagger"` do `"api/TodoItems"` :</span><span class="sxs-lookup"><span data-stu-id="067dc-200">In *Properties\launchSettings.json*, update `launchUrl` from `"swagger"` to `"api/TodoItems"`:</span></span>

```json
"launchUrl": "api/TodoItems",
```

<span data-ttu-id="067dc-201">Ze względu na to, że program Swagger został usunięty, poprzedzający znacznik zmienia adres URL, który jest uruchamiany w metodzie GET kontrolera dodanej w poniższych sekcjach.</span><span class="sxs-lookup"><span data-stu-id="067dc-201">Because Swagger has been removed, the preceding markup changes the URL that is launched to the GET method of the controller added in the following sections.</span></span>

## <a name="add-a-model-class"></a><span data-ttu-id="067dc-202">Dodaj klasę modelu</span><span class="sxs-lookup"><span data-stu-id="067dc-202">Add a model class</span></span>

<span data-ttu-id="067dc-203">*Model* to zestaw klas, które reprezentują dane zarządzane przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="067dc-203">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="067dc-204">Model tej aplikacji jest pojedynczą `TodoItem` klasą.</span><span class="sxs-lookup"><span data-stu-id="067dc-204">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="067dc-205">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="067dc-205">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="067dc-206">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt.</span><span class="sxs-lookup"><span data-stu-id="067dc-206">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="067dc-207">Wybierz pozycję **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="067dc-207">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="067dc-208">Nazwij folder *Models* .</span><span class="sxs-lookup"><span data-stu-id="067dc-208">Name the folder *Models*.</span></span>

* <span data-ttu-id="067dc-209">Kliknij prawym przyciskiem myszy *Models* folder i wybierz polecenie **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="067dc-209">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="067dc-210">Nadaj klasie nazwę *TodoItem* i wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="067dc-210">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="067dc-211">Zastąp kod szablonu następującym:</span><span class="sxs-lookup"><span data-stu-id="067dc-211">Replace the template code with the following:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="067dc-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="067dc-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="067dc-213">Dodaj folder o nazwie *Models* .</span><span class="sxs-lookup"><span data-stu-id="067dc-213">Add a folder named *Models*.</span></span>

* <span data-ttu-id="067dc-214">Dodaj `TodoItem` do *Models* folderu klasę o następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="067dc-214">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="067dc-215">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="067dc-215">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="067dc-216">Kliknij prawym przyciskiem myszy projekt.</span><span class="sxs-lookup"><span data-stu-id="067dc-216">Right-click the project.</span></span> <span data-ttu-id="067dc-217">Wybierz pozycję **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="067dc-217">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="067dc-218">Nazwij folder *Models* .</span><span class="sxs-lookup"><span data-stu-id="067dc-218">Name the folder *Models*.</span></span>

  ![Nowy folder](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="067dc-220">Kliknij prawym przyciskiem myszy *Models* folder, a następnie wybierz pozycję **Dodaj** > **nowy plik** > **ogólna** > **pusta Klasa**.</span><span class="sxs-lookup"><span data-stu-id="067dc-220">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="067dc-221">Nazwij klasę *TodoItem*, a następnie kliknij pozycję **New (nowy**).</span><span class="sxs-lookup"><span data-stu-id="067dc-221">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="067dc-222">Zastąp kod szablonu następującym:</span><span class="sxs-lookup"><span data-stu-id="067dc-222">Replace the template code with the following:</span></span>

---

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="067dc-223">`Id`Właściwość działa jako unikatowy klucz w relacyjnej bazie danych.</span><span class="sxs-lookup"><span data-stu-id="067dc-223">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="067dc-224">Klasy modelu mogą przejść do dowolnego miejsca w projekcie, ale *Models* folder jest używany przez Konwencję.</span><span class="sxs-lookup"><span data-stu-id="067dc-224">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="067dc-225">Dodawanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="067dc-225">Add a database context</span></span>

<span data-ttu-id="067dc-226">*Kontekst bazy danych* jest główną klasą, która koordynuje Entity Framework funkcji dla modelu danych.</span><span class="sxs-lookup"><span data-stu-id="067dc-226">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="067dc-227">Ta klasa jest tworzona przez wyprowadzanie z <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> klasy.</span><span class="sxs-lookup"><span data-stu-id="067dc-227">This class is created by deriving from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="067dc-228">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="067dc-228">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="067dc-229">Dodawanie pakietów NuGet</span><span class="sxs-lookup"><span data-stu-id="067dc-229">Add NuGet packages</span></span>

* <span data-ttu-id="067dc-230">W menu **Narzędzia** wybierz pozycję **menedżer pakietów NuGet > zarządzanie pakietami NuGet dla rozwiązania**.</span><span class="sxs-lookup"><span data-stu-id="067dc-230">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="067dc-231">Wybierz kartę **Przeglądaj** , a następnie w polu wyszukiwania wprowadź ciąg **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="067dc-231">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Delete this line at RTM -->
* <span data-ttu-id="067dc-232">W lewym okienku wybierz pozycję **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="067dc-232">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="067dc-233">Zaznacz pole wyboru **projekt** w prawym okienku, a następnie wybierz pozycję **Zainstaluj**.</span><span class="sxs-lookup"><span data-stu-id="067dc-233">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="067dc-234">Użyj powyższych instrukcji, aby dodać pakiet NuGet **Microsoft. EntityFrameworkCore. inMemory** .</span><span class="sxs-lookup"><span data-stu-id="067dc-234">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Update this image at RTM -->
<span data-ttu-id="067dc-235">![Menedżer pakietów NuGet](first-web-api/_static/5/vsNuGet.png)</span><span class="sxs-lookup"><span data-stu-id="067dc-235">![NuGet Package Manager](first-web-api/_static/5/vsNuGet.png)</span></span>

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="067dc-236">Dodawanie kontekstu bazy danych TodoContext</span><span class="sxs-lookup"><span data-stu-id="067dc-236">Add the TodoContext database context</span></span>

* <span data-ttu-id="067dc-237">Kliknij prawym przyciskiem myszy *Models* folder i wybierz polecenie **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="067dc-237">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="067dc-238">Nadaj klasie nazwę *TodoContext* i kliknij przycisk **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="067dc-238">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="067dc-239">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="067dc-239">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="067dc-240">Dodaj `TodoContext` klasę do *Models* folderu.</span><span class="sxs-lookup"><span data-stu-id="067dc-240">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="067dc-241">Wprowadź następujący kod:</span><span class="sxs-lookup"><span data-stu-id="067dc-241">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="067dc-242">Rejestrowanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="067dc-242">Register the database context</span></span>

<span data-ttu-id="067dc-243">W ASP.NET Core usługi, takie jak kontekst bazy danych, muszą być zarejestrowane z kontenerem [iniekcji zależności (di)](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="067dc-243">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="067dc-244">Kontener udostępnia usługę kontrolerom.</span><span class="sxs-lookup"><span data-stu-id="067dc-244">The container provides the service to controllers.</span></span>

<span data-ttu-id="067dc-245">Zaktualizuj *Startup.cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="067dc-245">Update *Startup.cs* with the following code:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="067dc-246">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="067dc-246">The preceding code:</span></span>

* <span data-ttu-id="067dc-247">Usuwa wywołania struktury Swagger.</span><span class="sxs-lookup"><span data-stu-id="067dc-247">Removes the Swagger calls.</span></span>
* <span data-ttu-id="067dc-248">Usuwa nieużywane `using` deklaracje.</span><span class="sxs-lookup"><span data-stu-id="067dc-248">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="067dc-249">Dodaje kontekst bazy danych do kontenera DI.</span><span class="sxs-lookup"><span data-stu-id="067dc-249">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="067dc-250">Określa, że kontekst bazy danych będzie używać bazy danych w pamięci.</span><span class="sxs-lookup"><span data-stu-id="067dc-250">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="067dc-251">Tworzenie szkieletu kontrolera</span><span class="sxs-lookup"><span data-stu-id="067dc-251">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="067dc-252">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="067dc-252">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="067dc-253">Kliknij prawym przyciskiem myszy folder *controllers* .</span><span class="sxs-lookup"><span data-stu-id="067dc-253">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="067dc-254">Wybierz pozycję **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="067dc-254">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="067dc-255">Wybierz pozycję **kontroler interfejsu API z akcjami, używając Entity Framework**, a następnie wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="067dc-255">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="067dc-256">Na stronie **Dodawanie kontrolera interfejsu API z akcjami przy użyciu Entity Framework** dialogowego:</span><span class="sxs-lookup"><span data-stu-id="067dc-256">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="067dc-257">Wybierz pozycję **TodoItem (TodoApi. Models )** w **klasie model**.</span><span class="sxs-lookup"><span data-stu-id="067dc-257">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="067dc-258">Wybierz pozycję **TodoContext (TodoApi. Models )** w **klasie kontekstu danych**.</span><span class="sxs-lookup"><span data-stu-id="067dc-258">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="067dc-259">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="067dc-259">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="067dc-260">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="067dc-260">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="067dc-261">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="067dc-261">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet tool update -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="067dc-262">Poprzednie polecenia:</span><span class="sxs-lookup"><span data-stu-id="067dc-262">The preceding commands:</span></span>

* <span data-ttu-id="067dc-263">Dodaj pakiety NuGet wymagane do tworzenia szkieletów.</span><span class="sxs-lookup"><span data-stu-id="067dc-263">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="067dc-264">Instaluje aparat tworzenia szkieletu ( `dotnet-aspnet-codegenerator` ).</span><span class="sxs-lookup"><span data-stu-id="067dc-264">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="067dc-265">Szkielety `TodoItemsController` .</span><span class="sxs-lookup"><span data-stu-id="067dc-265">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="067dc-266">Wygenerowany kod:</span><span class="sxs-lookup"><span data-stu-id="067dc-266">The generated code:</span></span>

* <span data-ttu-id="067dc-267">Oznacza klasę [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atrybutem.</span><span class="sxs-lookup"><span data-stu-id="067dc-267">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="067dc-268">Ten atrybut wskazuje, że kontroler odpowiada na żądania interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="067dc-268">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="067dc-269">Aby uzyskać informacje o określonych zachowaniach, które włącza atrybut, zobacz <xref:web-api/index> .</span><span class="sxs-lookup"><span data-stu-id="067dc-269">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="067dc-270">Używa funkcji DI do iniekcji kontekstu bazy danych ( `TodoContext` ) do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="067dc-270">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="067dc-271">Kontekst bazy danych jest używany w każdej z metod [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) w kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="067dc-271">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="067dc-272">Szablony ASP.NET Core dla:</span><span class="sxs-lookup"><span data-stu-id="067dc-272">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="067dc-273">Kontrolery z widokami obejmują `[action]` szablon trasy.</span><span class="sxs-lookup"><span data-stu-id="067dc-273">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="067dc-274">Kontrolery interfejsu API nie należą `[action]` do szablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="067dc-274">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="067dc-275">Gdy `[action]` token nie znajduje się w szablonie trasy, nazwa [akcji](xref:mvc/controllers/routing#action) jest wykluczona z trasy.</span><span class="sxs-lookup"><span data-stu-id="067dc-275">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="067dc-276">Oznacza to, że nazwa metody skojarzonej z akcją nie jest używana w zgodnej trasie.</span><span class="sxs-lookup"><span data-stu-id="067dc-276">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="update-the-posttodoitem-create-method"></a><span data-ttu-id="067dc-277">Aktualizowanie metody PostTodoItem Create</span><span class="sxs-lookup"><span data-stu-id="067dc-277">Update the PostTodoItem create method</span></span>

<span data-ttu-id="067dc-278">Zastąp instrukcję return w, `PostTodoItem` Aby użyć operatora [nameof](/dotnet/csharp/language-reference/operators/nameof) :</span><span class="sxs-lookup"><span data-stu-id="067dc-278">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="067dc-279">Poprzedni kod jest metodą POST protokołu HTTP, jak wskazano w [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) atrybucie.</span><span class="sxs-lookup"><span data-stu-id="067dc-279">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="067dc-280">Metoda pobiera wartość elementu do wykonania z treści żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="067dc-280">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="067dc-281">Aby uzyskać więcej informacji, zobacz temat [Routing atrybutów z atrybutami http [Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="067dc-281">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="067dc-282"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>Metoda:</span><span class="sxs-lookup"><span data-stu-id="067dc-282">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="067dc-283">W razie powodzenia zwraca [kod stanu HTTP 201](https://developer.mozilla.org/docs/Web/HTTP/Status/201) .</span><span class="sxs-lookup"><span data-stu-id="067dc-283">Returns an [HTTP 201 status code](https://developer.mozilla.org/docs/Web/HTTP/Status/201) if successful.</span></span> <span data-ttu-id="067dc-284">HTTP 201 to standardowa odpowiedź dla metody POST protokołu HTTP, która tworzy nowy zasób na serwerze.</span><span class="sxs-lookup"><span data-stu-id="067dc-284">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="067dc-285">Dodaje nagłówek [lokalizacji](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) do odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="067dc-285">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="067dc-286">`Location`Nagłówek określa [Identyfikator URI](https://developer.mozilla.org/docs/Glossary/URI) nowo utworzonego elementu do wykonania.</span><span class="sxs-lookup"><span data-stu-id="067dc-286">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="067dc-287">Aby uzyskać więcej informacji, zobacz [10.2.2 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="067dc-287">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="067dc-288">Odwołuje się do `GetTodoItem` akcji tworzenia `Location` identyfikatora URI nagłówka.</span><span class="sxs-lookup"><span data-stu-id="067dc-288">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="067dc-289">`nameof`Słowo kluczowe języka C# służy do zapobiegania twardemu kodowaniu nazwy akcji w `CreatedAtAction` wywołaniu.</span><span class="sxs-lookup"><span data-stu-id="067dc-289">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="067dc-290">Zainstaluj program Poster</span><span class="sxs-lookup"><span data-stu-id="067dc-290">Install Postman</span></span>

<span data-ttu-id="067dc-291">Ten samouczek używa programu do testowania interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="067dc-291">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="067dc-292">Zainstaluj program [Poster](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="067dc-292">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="067dc-293">Uruchom aplikację internetową.</span><span class="sxs-lookup"><span data-stu-id="067dc-293">Start the web app.</span></span>
* <span data-ttu-id="067dc-294">Uruchom wpis.</span><span class="sxs-lookup"><span data-stu-id="067dc-294">Start Postman.</span></span>
* <span data-ttu-id="067dc-295">Wyłącz **weryfikację certyfikatu SSL**</span><span class="sxs-lookup"><span data-stu-id="067dc-295">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="067dc-296">W  obszarze > **Ustawienia** pliku (karta **Ogólne** ) Wyłącz **weryfikację certyfikatu SSL**.</span><span class="sxs-lookup"><span data-stu-id="067dc-296">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="067dc-297">Po przetestowaniu kontrolera ponownie Włącz weryfikację certyfikatu SSL.</span><span class="sxs-lookup"><span data-stu-id="067dc-297">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="067dc-298">Test PostTodoItem za pomocą programu Poster</span><span class="sxs-lookup"><span data-stu-id="067dc-298">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="067dc-299">Utwórz nowe żądanie.</span><span class="sxs-lookup"><span data-stu-id="067dc-299">Create a new request.</span></span>
* <span data-ttu-id="067dc-300">Ustaw metodę HTTP na `POST` .</span><span class="sxs-lookup"><span data-stu-id="067dc-300">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="067dc-301">Ustaw identyfikator URI na `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="067dc-301">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="067dc-302">Na przykład `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="067dc-302">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="067dc-303">Wybierz kartę **Treść**.</span><span class="sxs-lookup"><span data-stu-id="067dc-303">Select the **Body** tab.</span></span>
* <span data-ttu-id="067dc-304">Wybierz przycisk radiowy **RAW** .</span><span class="sxs-lookup"><span data-stu-id="067dc-304">Select the **raw** radio button.</span></span>
* <span data-ttu-id="067dc-305">Ustaw typ na **JSON (Application/JSON)**.</span><span class="sxs-lookup"><span data-stu-id="067dc-305">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="067dc-306">W treści żądania wprowadź kod JSON dla elementu do wykonania:</span><span class="sxs-lookup"><span data-stu-id="067dc-306">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="067dc-307">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="067dc-307">Select **Send**.</span></span>

  ![Ogłoś przy użyciu żądania Create](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="067dc-309">Testowanie identyfikatora URI nagłówka lokalizacji</span><span class="sxs-lookup"><span data-stu-id="067dc-309">Test the location header URI</span></span>

<span data-ttu-id="067dc-310">Identyfikator URI nagłówka lokalizacji może zostać przetestowany w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="067dc-310">The location header URI can be tested in the browser.</span></span> <span data-ttu-id="067dc-311">Skopiuj i wklej identyfikator URI nagłówka lokalizacji do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="067dc-311">Copy and paste the location header URI into the browser.</span></span>

<span data-ttu-id="067dc-312">Aby przetestować w programie Poster:</span><span class="sxs-lookup"><span data-stu-id="067dc-312">To test in Postman:</span></span>

* <span data-ttu-id="067dc-313">Wybierz kartę **nagłówki** w okienku **odpowiedź** .</span><span class="sxs-lookup"><span data-stu-id="067dc-313">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="067dc-314">Skopiuj wartość nagłówka **lokalizacji** :</span><span class="sxs-lookup"><span data-stu-id="067dc-314">Copy the **Location** header value:</span></span>

  ![Karta nagłówki w konsoli programu Poster](first-web-api/_static/3/create.png)

* <span data-ttu-id="067dc-316">Ustaw metodę HTTP na `GET` .</span><span class="sxs-lookup"><span data-stu-id="067dc-316">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="067dc-317">Ustaw identyfikator URI na `https://localhost:<port>/api/TodoItems/1` .</span><span class="sxs-lookup"><span data-stu-id="067dc-317">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="067dc-318">Na przykład `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="067dc-318">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="067dc-319">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="067dc-319">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="067dc-320">Badanie metod GET</span><span class="sxs-lookup"><span data-stu-id="067dc-320">Examine the GET methods</span></span>

<span data-ttu-id="067dc-321">Zaimplementowane są dwa punkty końcowe GET:</span><span class="sxs-lookup"><span data-stu-id="067dc-321">Two GET endpoints are implemented:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="067dc-322">Przetestuj aplikację, wywołując dwa punkty końcowe z przeglądarki lub wpisu.</span><span class="sxs-lookup"><span data-stu-id="067dc-322">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="067dc-323">Przykład:</span><span class="sxs-lookup"><span data-stu-id="067dc-323">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="067dc-324">Odpowiedź podobna do poniższego jest generowana przez wywołanie `GetTodoItems` :</span><span class="sxs-lookup"><span data-stu-id="067dc-324">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="067dc-325">Test get przy użyciu programu Poster</span><span class="sxs-lookup"><span data-stu-id="067dc-325">Test Get with Postman</span></span>

* <span data-ttu-id="067dc-326">Utwórz nowe żądanie.</span><span class="sxs-lookup"><span data-stu-id="067dc-326">Create a new request.</span></span>
* <span data-ttu-id="067dc-327">Ustaw metodę HTTP, aby **uzyskać**.</span><span class="sxs-lookup"><span data-stu-id="067dc-327">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="067dc-328">Ustaw identyfikator URI żądania na `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="067dc-328">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="067dc-329">Na przykład `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="067dc-329">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="067dc-330">Ustaw **dwa widoki okienka** w programie Poster.</span><span class="sxs-lookup"><span data-stu-id="067dc-330">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="067dc-331">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="067dc-331">Select **Send**.</span></span>

<span data-ttu-id="067dc-332">Ta aplikacja używa bazy danych w pamięci.</span><span class="sxs-lookup"><span data-stu-id="067dc-332">This app uses an in-memory database.</span></span> <span data-ttu-id="067dc-333">Jeśli aplikacja zostanie zatrzymana i uruchomiona, poprzednie żądanie GET nie zwróci żadnych danych.</span><span class="sxs-lookup"><span data-stu-id="067dc-333">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="067dc-334">Jeśli nie zostaną zwrócone żadne dane, [Opublikuj](#post) dane w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="067dc-334">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="067dc-335">Ścieżki routingu i adresów URL</span><span class="sxs-lookup"><span data-stu-id="067dc-335">Routing and URL paths</span></span>

<span data-ttu-id="067dc-336">Ten [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) atrybut oznacza metodę, która reaguje na żądanie HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="067dc-336">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="067dc-337">Ścieżka adresu URL dla każdej metody jest zbudowana w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="067dc-337">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="067dc-338">Zacznij od ciągu szablonu w `Route` atrybucie kontrolera:</span><span class="sxs-lookup"><span data-stu-id="067dc-338">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="067dc-339">Zastąp `[controller]` nazwą kontrolera, którą Konwencją jest nazwa klasy kontrolera minus sufiks "Controller".</span><span class="sxs-lookup"><span data-stu-id="067dc-339">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="067dc-340">Dla tego przykładu nazwa klasy kontrolera to **TodoItems** Controller, więc nazwa kontrolera to "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="067dc-340">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="067dc-341">W ASP.NET Core [routingu](xref:mvc/controllers/routing) jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="067dc-341">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="067dc-342">Jeśli `[HttpGet]` atrybut ma szablon trasy (na przykład `[HttpGet("products")]` ), Dodaj go do ścieżki.</span><span class="sxs-lookup"><span data-stu-id="067dc-342">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="067dc-343">Ten przykład nie używa szablonu.</span><span class="sxs-lookup"><span data-stu-id="067dc-343">This sample doesn't use a template.</span></span> <span data-ttu-id="067dc-344">Aby uzyskać więcej informacji, zobacz temat [Routing atrybutów z atrybutami http [Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="067dc-344">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="067dc-345">W poniższej `GetTodoItem` metodzie `"{id}"` jest zmienną zastępczą dla unikatowego identyfikatora elementu do wykonania.</span><span class="sxs-lookup"><span data-stu-id="067dc-345">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="067dc-346">Gdy `GetTodoItem` jest wywoływana, wartość `"{id}"` w adresie URL jest podawana do metody w jej `id` parametrze.</span><span class="sxs-lookup"><span data-stu-id="067dc-346">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="067dc-347">Wartości zwracane</span><span class="sxs-lookup"><span data-stu-id="067dc-347">Return values</span></span>

<span data-ttu-id="067dc-348">Zwracany typ `GetTodoItems` `GetTodoItem` metod i jest [ \<T> typem ActionResult](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="067dc-348">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="067dc-349">ASP.NET Core automatycznie serializować obiektu do [formatu JSON](https://www.json.org/) i zapisuje kod JSON w treści komunikatu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="067dc-349">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="067dc-350">Kod odpowiedzi dla tego typu zwracanego to [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), przy założeniu, że nie istnieją Nieobsłużone wyjątki.</span><span class="sxs-lookup"><span data-stu-id="067dc-350">The response code for this return type is [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="067dc-351">Nieobsłużone wyjątki są tłumaczone na błędy 5xx.</span><span class="sxs-lookup"><span data-stu-id="067dc-351">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="067dc-352">`ActionResult` typy zwracane mogą reprezentować szeroką gamę kodów stanu HTTP.</span><span class="sxs-lookup"><span data-stu-id="067dc-352">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="067dc-353">Na przykład `GetTodoItem` może zwracać dwie różne wartości stanu:</span><span class="sxs-lookup"><span data-stu-id="067dc-353">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="067dc-354">Jeśli żaden element nie jest zgodny z żądanym IDENTYFIKATORem, metoda zwraca kod błędu [stanu 404](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> .</span><span class="sxs-lookup"><span data-stu-id="067dc-354">If no item matches the requested ID, the method returns a [404 status](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="067dc-355">W przeciwnym razie metoda zwraca 200 z treścią odpowiedzi JSON.</span><span class="sxs-lookup"><span data-stu-id="067dc-355">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="067dc-356">Zwracanie `item` wyników w odpowiedzi HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="067dc-356">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="067dc-357">Metoda PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="067dc-357">The PutTodoItem method</span></span>

<span data-ttu-id="067dc-358">Przeanalizuj metodę `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="067dc-358">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="067dc-359">`PutTodoItem` jest podobny do `PostTodoItem` , z tą różnicą, że używa protokołu HTTP Put.</span><span class="sxs-lookup"><span data-stu-id="067dc-359">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="067dc-360">Odpowiedź to [204 (brak zawartości)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="067dc-360">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="067dc-361">Zgodnie ze specyfikacją protokołu HTTP żądanie PUT wymaga, aby klient wysłał całą zaktualizowaną jednostkę, a nie tylko te zmiany.</span><span class="sxs-lookup"><span data-stu-id="067dc-361">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="067dc-362">Aby zapewnić obsługę częściowych aktualizacji, użyj [poprawki http](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="067dc-362">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="067dc-363">Jeśli wystąpi błąd podczas wywoływania `PutTodoItem` , wywołaj, `GET` Aby upewnić się, że w bazie danych znajduje się element.</span><span class="sxs-lookup"><span data-stu-id="067dc-363">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="067dc-364">Testowanie metody PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="067dc-364">Test the PutTodoItem method</span></span>

<span data-ttu-id="067dc-365">Ten przykład korzysta z bazy danych w pamięci, która musi zostać zainicjowana za każdym razem, gdy aplikacja zostanie uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="067dc-365">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="067dc-366">Przed wykonaniem wywołania PUT musi istnieć element w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="067dc-366">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="067dc-367">Przed wykonaniem wywołania PUT należy wywołać metodę GET, aby upewnić się, że istnieje element w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="067dc-367">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="067dc-368">Zaktualizuj element do wykonania o identyfikatorze 1 i ustaw jego nazwę na `"feed fish"` :</span><span class="sxs-lookup"><span data-stu-id="067dc-368">Update the to-do item that has Id = 1 and set its name to `"feed fish"`:</span></span>

```json
  {
    "Id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="067dc-369">Na poniższej ilustracji przedstawiono aktualizację programu Poster:</span><span class="sxs-lookup"><span data-stu-id="067dc-369">The following image shows the Postman update:</span></span>

![Konsola programu Poster pokazująca odpowiedź 204 (brak zawartości)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="067dc-371">Metoda DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="067dc-371">The DeleteTodoItem method</span></span>

<span data-ttu-id="067dc-372">Przeanalizuj metodę `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="067dc-372">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="067dc-373">Testowanie metody DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="067dc-373">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="067dc-374">Użyj programu Poster, aby usunąć element do wykonania:</span><span class="sxs-lookup"><span data-stu-id="067dc-374">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="067dc-375">Ustaw metodę na `DELETE` .</span><span class="sxs-lookup"><span data-stu-id="067dc-375">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="067dc-376">Ustaw identyfikator URI obiektu do usunięcia (na przykład `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="067dc-376">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="067dc-377">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="067dc-377">Select **Send**.</span></span>

<a name="over-post-v5"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="067dc-378">Zapobiegaj za pośrednictwem księgowania</span><span class="sxs-lookup"><span data-stu-id="067dc-378">Prevent over-posting</span></span>

<span data-ttu-id="067dc-379">Obecnie Przykładowa aplikacja uwidacznia cały `TodoItem` obiekt.</span><span class="sxs-lookup"><span data-stu-id="067dc-379">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="067dc-380">Aplikacje produkcyjne zwykle ograniczają dane wejściowe i zwracane przy użyciu podzestawu modelu.</span><span class="sxs-lookup"><span data-stu-id="067dc-380">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="067dc-381">Istnieje wiele powodów związanych z tym, a zabezpieczenia są głównymi.</span><span class="sxs-lookup"><span data-stu-id="067dc-381">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="067dc-382">Podzestaw modelu jest zwykle określany jako obiekt Transfer danych (DTO), model wejściowy lub model widoku.</span><span class="sxs-lookup"><span data-stu-id="067dc-382">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="067dc-383">**DTO** jest używany w tym artykule.</span><span class="sxs-lookup"><span data-stu-id="067dc-383">**DTO** is used in this article.</span></span>

<span data-ttu-id="067dc-384">DTO może służyć do:</span><span class="sxs-lookup"><span data-stu-id="067dc-384">A DTO may be used to:</span></span>

* <span data-ttu-id="067dc-385">Zablokuj nadmierne księgowanie.</span><span class="sxs-lookup"><span data-stu-id="067dc-385">Prevent over-posting.</span></span>
* <span data-ttu-id="067dc-386">Ukryj właściwości, które nie powinny być wyświetlane dla klientów.</span><span class="sxs-lookup"><span data-stu-id="067dc-386">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="067dc-387">Pomiń niektóre właściwości, aby zmniejszyć rozmiar ładunku.</span><span class="sxs-lookup"><span data-stu-id="067dc-387">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="067dc-388">Spłaszcz wykresy obiektów zawierające obiekty zagnieżdżone.</span><span class="sxs-lookup"><span data-stu-id="067dc-388">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="067dc-389">Spłaszczone wykresy obiektów mogą być wygodniejsze dla klientów.</span><span class="sxs-lookup"><span data-stu-id="067dc-389">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="067dc-390">Aby zademonstrować podejście DTO, zaktualizuj `TodoItem` klasę w celu uwzględnienia pola tajnego:</span><span class="sxs-lookup"><span data-stu-id="067dc-390">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=8)]

<span data-ttu-id="067dc-391">Pole tajne musi być ukryte w tej aplikacji, ale aplikacja administracyjna mogła ją uwidocznić.</span><span class="sxs-lookup"><span data-stu-id="067dc-391">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="067dc-392">Sprawdź, czy można opublikować i pobrać pole tajne.</span><span class="sxs-lookup"><span data-stu-id="067dc-392">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="067dc-393">Utwórz model DTO:</span><span class="sxs-lookup"><span data-stu-id="067dc-393">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="067dc-394">Zaktualizuj, `TodoItemsController` Aby użyć `TodoItemDTO` :</span><span class="sxs-lookup"><span data-stu-id="067dc-394">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="067dc-395">Upewnij się, że nie można opublikować lub pobrać pola tajnego.</span><span class="sxs-lookup"><span data-stu-id="067dc-395">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="067dc-396">Wywoływanie interfejsu API sieci Web przy użyciu języka JavaScript</span><span class="sxs-lookup"><span data-stu-id="067dc-396">Call the web API with JavaScript</span></span>

<span data-ttu-id="067dc-397">Zobacz [Samouczek: wywoływanie interfejsu API sieci web ASP.NET Core przy użyciu języka JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="067dc-397">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="067dc-398">Ten samouczek zawiera informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="067dc-398">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="067dc-399">Utwórz projekt interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="067dc-399">Create a web API project.</span></span>
> * <span data-ttu-id="067dc-400">Dodaj klasę modelu i kontekst bazy danych.</span><span class="sxs-lookup"><span data-stu-id="067dc-400">Add a model class and a database context.</span></span>
> * <span data-ttu-id="067dc-401">Tworzy szkielet kontrolera z metodami CRUD.</span><span class="sxs-lookup"><span data-stu-id="067dc-401">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="067dc-402">Skonfiguruj Routing, ścieżki URL i wartości zwracane.</span><span class="sxs-lookup"><span data-stu-id="067dc-402">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="067dc-403">Wywołaj interfejs API sieci Web za pomocą programu Poster.</span><span class="sxs-lookup"><span data-stu-id="067dc-403">Call the web API with Postman.</span></span>

<span data-ttu-id="067dc-404">Na końcu znajduje się internetowy interfejs API, który może zarządzać elementami do wykonania przechowywanymi w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="067dc-404">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="067dc-405">Omówienie</span><span class="sxs-lookup"><span data-stu-id="067dc-405">Overview</span></span>

<span data-ttu-id="067dc-406">Ten samouczek tworzy następujący interfejs API:</span><span class="sxs-lookup"><span data-stu-id="067dc-406">This tutorial creates the following API:</span></span>

|<span data-ttu-id="067dc-407">Interfejs API</span><span class="sxs-lookup"><span data-stu-id="067dc-407">API</span></span> | <span data-ttu-id="067dc-408">Opis</span><span class="sxs-lookup"><span data-stu-id="067dc-408">Description</span></span> | <span data-ttu-id="067dc-409">Treść żądania</span><span class="sxs-lookup"><span data-stu-id="067dc-409">Request body</span></span> | <span data-ttu-id="067dc-410">Treść odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="067dc-410">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="067dc-411">Pobierz wszystkie elementy do wykonania</span><span class="sxs-lookup"><span data-stu-id="067dc-411">Get all to-do items</span></span> | <span data-ttu-id="067dc-412">Brak</span><span class="sxs-lookup"><span data-stu-id="067dc-412">None</span></span> | <span data-ttu-id="067dc-413">Tablica elementów do wykonania</span><span class="sxs-lookup"><span data-stu-id="067dc-413">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="067dc-414">Pobieranie elementu według identyfikatora</span><span class="sxs-lookup"><span data-stu-id="067dc-414">Get an item by ID</span></span> | <span data-ttu-id="067dc-415">Brak</span><span class="sxs-lookup"><span data-stu-id="067dc-415">None</span></span> | <span data-ttu-id="067dc-416">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="067dc-416">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="067dc-417">Dodaj nowy element</span><span class="sxs-lookup"><span data-stu-id="067dc-417">Add a new item</span></span> | <span data-ttu-id="067dc-418">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="067dc-418">To-do item</span></span> | <span data-ttu-id="067dc-419">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="067dc-419">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="067dc-420">Aktualizowanie istniejącego elementu &nbsp;</span><span class="sxs-lookup"><span data-stu-id="067dc-420">Update an existing item &nbsp;</span></span> | <span data-ttu-id="067dc-421">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="067dc-421">To-do item</span></span> | <span data-ttu-id="067dc-422">Brak</span><span class="sxs-lookup"><span data-stu-id="067dc-422">None</span></span> |
|<span data-ttu-id="067dc-423">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="067dc-423">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="067dc-424">Usuń element &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="067dc-424">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="067dc-425">Brak</span><span class="sxs-lookup"><span data-stu-id="067dc-425">None</span></span> | <span data-ttu-id="067dc-426">Brak</span><span class="sxs-lookup"><span data-stu-id="067dc-426">None</span></span>|

<span data-ttu-id="067dc-427">Na poniższym diagramie przedstawiono projekt aplikacji.</span><span class="sxs-lookup"><span data-stu-id="067dc-427">The following diagram shows the design of the app.</span></span>

![Klient jest reprezentowany przez pole po lewej stronie.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="067dc-433">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="067dc-433">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="067dc-434">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="067dc-434">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="067dc-435">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="067dc-435">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="067dc-436">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="067dc-436">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="067dc-437">Tworzenie projektu sieci Web</span><span class="sxs-lookup"><span data-stu-id="067dc-437">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="067dc-438">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="067dc-438">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="067dc-439">Z menu **plik** wybierz pozycję **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="067dc-439">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="067dc-440">Wybierz szablon **aplikacja sieci Web ASP.NET Core** a następnie kliknij przycisk **dalej**.</span><span class="sxs-lookup"><span data-stu-id="067dc-440">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="067dc-441">Nazwij projekt *TodoApi* i kliknij pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="067dc-441">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="067dc-442">W oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** upewnij się, że wybrano opcję **.net Core** i **ASP.NET Core 3,1** .</span><span class="sxs-lookup"><span data-stu-id="067dc-442">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="067dc-443">Wybierz szablon **interfejsu API** i kliknij przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="067dc-443">Select the **API** template and click **Create**.</span></span>

![Okno dialogowe programu VS New Project](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="067dc-445">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="067dc-445">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="067dc-446">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="067dc-446">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="067dc-447">Zmień katalog ( `cd` ) do folderu, który będzie zawierać folder projektu.</span><span class="sxs-lookup"><span data-stu-id="067dc-447">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="067dc-448">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="067dc-448">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="067dc-449">Gdy zostanie wyświetlone okno dialogowe z pytaniem, czy chcesz dodać wymagane zasoby do projektu, wybierz opcję **tak**.</span><span class="sxs-lookup"><span data-stu-id="067dc-449">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="067dc-450">Poprzednie polecenia:</span><span class="sxs-lookup"><span data-stu-id="067dc-450">The preceding commands:</span></span>

  * <span data-ttu-id="067dc-451">Tworzy nowy projekt internetowego interfejsu API i otwiera go w Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="067dc-451">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="067dc-452">Dodaje pakiety NuGet, które są wymagane w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="067dc-452">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="067dc-453">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="067dc-453">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="067dc-454">Wybierz pozycję **plik** > **nowe rozwiązanie**.</span><span class="sxs-lookup"><span data-stu-id="067dc-454">Select **File** > **New Solution**.</span></span>

  ![macOS nowe rozwiązanie](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="067dc-456">W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz pozycję  >    >  **interfejs API** aplikacji .NET Core  >  .</span><span class="sxs-lookup"><span data-stu-id="067dc-456">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="067dc-457">W wersji 8,6 lub nowszej wybierz pozycję   >    >  **interfejs API**  >  aplikacji sieci Web i konsoli.</span><span class="sxs-lookup"><span data-stu-id="067dc-457">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![Wybór szablonu interfejsu API macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="067dc-459">W oknie dialogowym **Konfigurowanie nowego interfejsu API sieci Web ASP.NET Core** wybierz najnowszą platformę **docelową**.NET Core 3. x.</span><span class="sxs-lookup"><span data-stu-id="067dc-459">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="067dc-460">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="067dc-460">Select **Next**.</span></span>

* <span data-ttu-id="067dc-461">Wprowadź *TodoApi* jako **nazwę projektu** , a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="067dc-461">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![okno dialogowe konfiguracji](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="067dc-463">Otwórz Terminal poleceń w folderze projektu i uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="067dc-463">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="067dc-464">Testowanie interfejsu API</span><span class="sxs-lookup"><span data-stu-id="067dc-464">Test the API</span></span>

<span data-ttu-id="067dc-465">Szablon projektu tworzy `WeatherForecast` interfejs API.</span><span class="sxs-lookup"><span data-stu-id="067dc-465">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="067dc-466">Wywołaj `Get` metodę z przeglądarki, aby przetestować aplikację.</span><span class="sxs-lookup"><span data-stu-id="067dc-466">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="067dc-467">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="067dc-467">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="067dc-468">Naciśnij klawisze CTRL + F5, aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="067dc-468">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="067dc-469">Program Visual Studio uruchamia przeglądarkę i przechodzi do `https://localhost:<port>/WeatherForecast` lokalizacji, gdzie `<port>` jest losowo wybierany numer portu.</span><span class="sxs-lookup"><span data-stu-id="067dc-469">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="067dc-470">Jeśli zostanie wyświetlone okno dialogowe z pytaniem, czy należy zaufać certyfikatowi IIS Express, wybierz pozycję **tak**.</span><span class="sxs-lookup"><span data-stu-id="067dc-470">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="067dc-471">W wyświetlonym oknie dialogowym **ostrzeżenia o zabezpieczeniach** wybierz pozycję **tak**.</span><span class="sxs-lookup"><span data-stu-id="067dc-471">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="067dc-472">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="067dc-472">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="067dc-473">Naciśnij klawisze CTRL + F5, aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="067dc-473">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="067dc-474">W przeglądarce przejdź do następującego adresu URL: `https://localhost:5001/WeatherForecast` .</span><span class="sxs-lookup"><span data-stu-id="067dc-474">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="067dc-475">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="067dc-475">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="067dc-476">Wybierz pozycję **Uruchom**  >  **Rozpocznij debugowanie** , aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="067dc-476">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="067dc-477">Visual Studio dla komputerów Mac uruchamia przeglądarkę i nawiguje do `https://localhost:<port>` , gdzie `<port>` jest losowo wybranym numerem portu.</span><span class="sxs-lookup"><span data-stu-id="067dc-477">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="067dc-478">Zwracany jest błąd HTTP 404 (nie znaleziono).</span><span class="sxs-lookup"><span data-stu-id="067dc-478">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="067dc-479">Dołącz `/WeatherForecast` do adresu URL (Zmień adres URL na `https://localhost:<port>/WeatherForecast` ).</span><span class="sxs-lookup"><span data-stu-id="067dc-479">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="067dc-480">Zwracany jest kod JSON podobny do następującego:</span><span class="sxs-lookup"><span data-stu-id="067dc-480">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="067dc-481">Dodaj klasę modelu</span><span class="sxs-lookup"><span data-stu-id="067dc-481">Add a model class</span></span>

<span data-ttu-id="067dc-482">*Model* to zestaw klas, które reprezentują dane zarządzane przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="067dc-482">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="067dc-483">Model tej aplikacji jest pojedynczą `TodoItem` klasą.</span><span class="sxs-lookup"><span data-stu-id="067dc-483">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="067dc-484">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="067dc-484">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="067dc-485">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt.</span><span class="sxs-lookup"><span data-stu-id="067dc-485">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="067dc-486">Wybierz pozycję **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="067dc-486">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="067dc-487">Nazwij folder *Models* .</span><span class="sxs-lookup"><span data-stu-id="067dc-487">Name the folder *Models*.</span></span>

* <span data-ttu-id="067dc-488">Kliknij prawym przyciskiem myszy *Models* folder i wybierz polecenie **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="067dc-488">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="067dc-489">Nadaj klasie nazwę *TodoItem* i wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="067dc-489">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="067dc-490">Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="067dc-490">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="067dc-491">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="067dc-491">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="067dc-492">Dodaj folder o nazwie *Models* .</span><span class="sxs-lookup"><span data-stu-id="067dc-492">Add a folder named *Models*.</span></span>

* <span data-ttu-id="067dc-493">Dodaj `TodoItem` do *Models* folderu klasę o następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="067dc-493">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="067dc-494">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="067dc-494">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="067dc-495">Kliknij prawym przyciskiem myszy projekt.</span><span class="sxs-lookup"><span data-stu-id="067dc-495">Right-click the project.</span></span> <span data-ttu-id="067dc-496">Wybierz pozycję **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="067dc-496">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="067dc-497">Nazwij folder *Models* .</span><span class="sxs-lookup"><span data-stu-id="067dc-497">Name the folder *Models*.</span></span>

  ![Nowy folder](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="067dc-499">Kliknij prawym przyciskiem myszy *Models* folder, a następnie wybierz pozycję **Dodaj** > **nowy plik** > **ogólna** > **pusta Klasa**.</span><span class="sxs-lookup"><span data-stu-id="067dc-499">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="067dc-500">Nazwij klasę *TodoItem*, a następnie kliknij pozycję **New (nowy**).</span><span class="sxs-lookup"><span data-stu-id="067dc-500">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="067dc-501">Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="067dc-501">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="067dc-502">`Id`Właściwość działa jako unikatowy klucz w relacyjnej bazie danych.</span><span class="sxs-lookup"><span data-stu-id="067dc-502">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="067dc-503">Klasy modelu mogą przejść do dowolnego miejsca w projekcie, ale *Models* folder jest używany przez Konwencję.</span><span class="sxs-lookup"><span data-stu-id="067dc-503">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="067dc-504">Dodawanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="067dc-504">Add a database context</span></span>

<span data-ttu-id="067dc-505">*Kontekst bazy danych* jest główną klasą, która koordynuje Entity Framework funkcji dla modelu danych.</span><span class="sxs-lookup"><span data-stu-id="067dc-505">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="067dc-506">Ta klasa jest tworzona przez wyprowadzanie z `Microsoft.EntityFrameworkCore.DbContext` klasy.</span><span class="sxs-lookup"><span data-stu-id="067dc-506">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="067dc-507">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="067dc-507">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="067dc-508">Dodawanie pakietów NuGet</span><span class="sxs-lookup"><span data-stu-id="067dc-508">Add NuGet packages</span></span>

* <span data-ttu-id="067dc-509">W menu **Narzędzia** wybierz pozycję **menedżer pakietów NuGet > zarządzanie pakietami NuGet dla rozwiązania**.</span><span class="sxs-lookup"><span data-stu-id="067dc-509">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="067dc-510">Wybierz kartę **Przeglądaj** , a następnie w polu wyszukiwania wprowadź ciąg **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="067dc-510">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="067dc-511">W lewym okienku wybierz pozycję **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="067dc-511">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="067dc-512">Zaznacz pole wyboru **projekt** w prawym okienku, a następnie wybierz pozycję **Zainstaluj**.</span><span class="sxs-lookup"><span data-stu-id="067dc-512">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="067dc-513">Użyj powyższych instrukcji, aby dodać pakiet NuGet **Microsoft. EntityFrameworkCore. inMemory** .</span><span class="sxs-lookup"><span data-stu-id="067dc-513">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

![Menedżer pakietów NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="067dc-515">Dodawanie kontekstu bazy danych TodoContext</span><span class="sxs-lookup"><span data-stu-id="067dc-515">Add the TodoContext database context</span></span>

* <span data-ttu-id="067dc-516">Kliknij prawym przyciskiem myszy *Models* folder i wybierz polecenie **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="067dc-516">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="067dc-517">Nadaj klasie nazwę *TodoContext* i kliknij przycisk **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="067dc-517">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="067dc-518">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="067dc-518">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="067dc-519">Dodaj `TodoContext` klasę do *Models* folderu.</span><span class="sxs-lookup"><span data-stu-id="067dc-519">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="067dc-520">Wprowadź następujący kod:</span><span class="sxs-lookup"><span data-stu-id="067dc-520">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="067dc-521">Rejestrowanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="067dc-521">Register the database context</span></span>

<span data-ttu-id="067dc-522">W ASP.NET Core usługi, takie jak kontekst bazy danych, muszą być zarejestrowane z kontenerem [iniekcji zależności (di)](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="067dc-522">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="067dc-523">Kontener udostępnia usługę kontrolerom.</span><span class="sxs-lookup"><span data-stu-id="067dc-523">The container provides the service to controllers.</span></span>

<span data-ttu-id="067dc-524">Zaktualizuj *Startup.cs* o następujący wyróżniony kod:</span><span class="sxs-lookup"><span data-stu-id="067dc-524">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="067dc-525">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="067dc-525">The preceding code:</span></span>

* <span data-ttu-id="067dc-526">Usuwa nieużywane `using` deklaracje.</span><span class="sxs-lookup"><span data-stu-id="067dc-526">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="067dc-527">Dodaje kontekst bazy danych do kontenera DI.</span><span class="sxs-lookup"><span data-stu-id="067dc-527">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="067dc-528">Określa, że kontekst bazy danych będzie używać bazy danych w pamięci.</span><span class="sxs-lookup"><span data-stu-id="067dc-528">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="067dc-529">Tworzenie szkieletu kontrolera</span><span class="sxs-lookup"><span data-stu-id="067dc-529">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="067dc-530">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="067dc-530">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="067dc-531">Kliknij prawym przyciskiem myszy folder *controllers* .</span><span class="sxs-lookup"><span data-stu-id="067dc-531">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="067dc-532">Wybierz pozycję **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="067dc-532">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="067dc-533">Wybierz pozycję **kontroler interfejsu API z akcjami, używając Entity Framework**, a następnie wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="067dc-533">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="067dc-534">Na stronie **Dodawanie kontrolera interfejsu API z akcjami przy użyciu Entity Framework** dialogowego:</span><span class="sxs-lookup"><span data-stu-id="067dc-534">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="067dc-535">Wybierz pozycję **TodoItem (TodoApi. Models )** w **klasie model**.</span><span class="sxs-lookup"><span data-stu-id="067dc-535">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="067dc-536">Wybierz pozycję **TodoContext (TodoApi. Models )** w **klasie kontekstu danych**.</span><span class="sxs-lookup"><span data-stu-id="067dc-536">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="067dc-537">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="067dc-537">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="067dc-538">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="067dc-538">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="067dc-539">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="067dc-539">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="067dc-540">Poprzednie polecenia:</span><span class="sxs-lookup"><span data-stu-id="067dc-540">The preceding commands:</span></span>

* <span data-ttu-id="067dc-541">Dodaj pakiety NuGet wymagane do tworzenia szkieletów.</span><span class="sxs-lookup"><span data-stu-id="067dc-541">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="067dc-542">Instaluje aparat tworzenia szkieletu ( `dotnet-aspnet-codegenerator` ).</span><span class="sxs-lookup"><span data-stu-id="067dc-542">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="067dc-543">Szkielety `TodoItemsController` .</span><span class="sxs-lookup"><span data-stu-id="067dc-543">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="067dc-544">Wygenerowany kod:</span><span class="sxs-lookup"><span data-stu-id="067dc-544">The generated code:</span></span>

* <span data-ttu-id="067dc-545">Oznacza klasę [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atrybutem.</span><span class="sxs-lookup"><span data-stu-id="067dc-545">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="067dc-546">Ten atrybut wskazuje, że kontroler odpowiada na żądania interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="067dc-546">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="067dc-547">Aby uzyskać informacje o określonych zachowaniach, które włącza atrybut, zobacz <xref:web-api/index> .</span><span class="sxs-lookup"><span data-stu-id="067dc-547">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="067dc-548">Używa funkcji DI do iniekcji kontekstu bazy danych ( `TodoContext` ) do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="067dc-548">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="067dc-549">Kontekst bazy danych jest używany w każdej z metod [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) w kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="067dc-549">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="067dc-550">Szablony ASP.NET Core dla:</span><span class="sxs-lookup"><span data-stu-id="067dc-550">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="067dc-551">Kontrolery z widokami obejmują `[action]` szablon trasy.</span><span class="sxs-lookup"><span data-stu-id="067dc-551">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="067dc-552">Kontrolery interfejsu API nie należą `[action]` do szablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="067dc-552">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="067dc-553">Gdy `[action]` token nie znajduje się w szablonie trasy, nazwa [akcji](xref:mvc/controllers/routing#action) jest wykluczona z trasy.</span><span class="sxs-lookup"><span data-stu-id="067dc-553">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="067dc-554">Oznacza to, że nazwa metody skojarzonej z akcją nie jest używana w zgodnej trasie.</span><span class="sxs-lookup"><span data-stu-id="067dc-554">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="067dc-555">Badanie metody PostTodoItem Create</span><span class="sxs-lookup"><span data-stu-id="067dc-555">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="067dc-556">Zastąp instrukcję return w, `PostTodoItem` Aby użyć operatora [nameof](/dotnet/csharp/language-reference/operators/nameof) :</span><span class="sxs-lookup"><span data-stu-id="067dc-556">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="067dc-557">Poprzedni kod jest metodą POST protokołu HTTP, jak wskazano w [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) atrybucie.</span><span class="sxs-lookup"><span data-stu-id="067dc-557">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="067dc-558">Metoda pobiera wartość elementu do wykonania z treści żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="067dc-558">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="067dc-559">Aby uzyskać więcej informacji, zobacz temat [Routing atrybutów z atrybutami http [Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="067dc-559">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="067dc-560"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>Metoda:</span><span class="sxs-lookup"><span data-stu-id="067dc-560">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="067dc-561">W razie powodzenia zwraca kod stanu HTTP 201.</span><span class="sxs-lookup"><span data-stu-id="067dc-561">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="067dc-562">HTTP 201 to standardowa odpowiedź dla metody POST protokołu HTTP, która tworzy nowy zasób na serwerze.</span><span class="sxs-lookup"><span data-stu-id="067dc-562">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="067dc-563">Dodaje nagłówek [lokalizacji](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) do odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="067dc-563">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="067dc-564">`Location`Nagłówek określa [Identyfikator URI](https://developer.mozilla.org/docs/Glossary/URI) nowo utworzonego elementu do wykonania.</span><span class="sxs-lookup"><span data-stu-id="067dc-564">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="067dc-565">Aby uzyskać więcej informacji, zobacz [10.2.2 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="067dc-565">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="067dc-566">Odwołuje się do `GetTodoItem` akcji tworzenia `Location` identyfikatora URI nagłówka.</span><span class="sxs-lookup"><span data-stu-id="067dc-566">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="067dc-567">`nameof`Słowo kluczowe języka C# służy do zapobiegania twardemu kodowaniu nazwy akcji w `CreatedAtAction` wywołaniu.</span><span class="sxs-lookup"><span data-stu-id="067dc-567">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="067dc-568">Zainstaluj program Poster</span><span class="sxs-lookup"><span data-stu-id="067dc-568">Install Postman</span></span>

<span data-ttu-id="067dc-569">Ten samouczek używa programu do testowania interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="067dc-569">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="067dc-570">Zainstaluj program [Poster](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="067dc-570">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="067dc-571">Uruchom aplikację internetową.</span><span class="sxs-lookup"><span data-stu-id="067dc-571">Start the web app.</span></span>
* <span data-ttu-id="067dc-572">Uruchom wpis.</span><span class="sxs-lookup"><span data-stu-id="067dc-572">Start Postman.</span></span>
* <span data-ttu-id="067dc-573">Wyłącz **weryfikację certyfikatu SSL**</span><span class="sxs-lookup"><span data-stu-id="067dc-573">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="067dc-574">W  obszarze > **Ustawienia** pliku (karta **Ogólne** ) Wyłącz **weryfikację certyfikatu SSL**.</span><span class="sxs-lookup"><span data-stu-id="067dc-574">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="067dc-575">Po przetestowaniu kontrolera ponownie Włącz weryfikację certyfikatu SSL.</span><span class="sxs-lookup"><span data-stu-id="067dc-575">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="067dc-576">Test PostTodoItem za pomocą programu Poster</span><span class="sxs-lookup"><span data-stu-id="067dc-576">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="067dc-577">Utwórz nowe żądanie.</span><span class="sxs-lookup"><span data-stu-id="067dc-577">Create a new request.</span></span>
* <span data-ttu-id="067dc-578">Ustaw metodę HTTP na `POST` .</span><span class="sxs-lookup"><span data-stu-id="067dc-578">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="067dc-579">Ustaw identyfikator URI na `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="067dc-579">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="067dc-580">Na przykład `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="067dc-580">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="067dc-581">Wybierz kartę **Treść**.</span><span class="sxs-lookup"><span data-stu-id="067dc-581">Select the **Body** tab.</span></span>
* <span data-ttu-id="067dc-582">Wybierz przycisk radiowy **RAW** .</span><span class="sxs-lookup"><span data-stu-id="067dc-582">Select the **raw** radio button.</span></span>
* <span data-ttu-id="067dc-583">Ustaw typ na **JSON (Application/JSON)**.</span><span class="sxs-lookup"><span data-stu-id="067dc-583">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="067dc-584">W treści żądania wprowadź kod JSON dla elementu do wykonania:</span><span class="sxs-lookup"><span data-stu-id="067dc-584">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="067dc-585">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="067dc-585">Select **Send**.</span></span>

  ![Ogłoś przy użyciu żądania Create](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a><span data-ttu-id="067dc-587">Testowanie identyfikatora URI nagłówka lokalizacji za pomocą programu Poster</span><span class="sxs-lookup"><span data-stu-id="067dc-587">Test the location header URI with Postman</span></span>

* <span data-ttu-id="067dc-588">Wybierz kartę **nagłówki** w okienku **odpowiedź** .</span><span class="sxs-lookup"><span data-stu-id="067dc-588">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="067dc-589">Skopiuj wartość nagłówka **lokalizacji** :</span><span class="sxs-lookup"><span data-stu-id="067dc-589">Copy the **Location** header value:</span></span>

  ![Karta nagłówki w konsoli programu Poster](first-web-api/_static/3/create.png)

* <span data-ttu-id="067dc-591">Ustaw metodę HTTP na `GET` .</span><span class="sxs-lookup"><span data-stu-id="067dc-591">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="067dc-592">Ustaw identyfikator URI na `https://localhost:<port>/api/TodoItems/1` .</span><span class="sxs-lookup"><span data-stu-id="067dc-592">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="067dc-593">Na przykład `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="067dc-593">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="067dc-594">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="067dc-594">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="067dc-595">Badanie metod GET</span><span class="sxs-lookup"><span data-stu-id="067dc-595">Examine the GET methods</span></span>

<span data-ttu-id="067dc-596">Te metody implementują dwa punkty końcowe GET:</span><span class="sxs-lookup"><span data-stu-id="067dc-596">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="067dc-597">Przetestuj aplikację, wywołując dwa punkty końcowe z przeglądarki lub wpisu.</span><span class="sxs-lookup"><span data-stu-id="067dc-597">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="067dc-598">Przykład:</span><span class="sxs-lookup"><span data-stu-id="067dc-598">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="067dc-599">Odpowiedź podobna do poniższego jest generowana przez wywołanie `GetTodoItems` :</span><span class="sxs-lookup"><span data-stu-id="067dc-599">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="067dc-600">Test get przy użyciu programu Poster</span><span class="sxs-lookup"><span data-stu-id="067dc-600">Test Get with Postman</span></span>

* <span data-ttu-id="067dc-601">Utwórz nowe żądanie.</span><span class="sxs-lookup"><span data-stu-id="067dc-601">Create a new request.</span></span>
* <span data-ttu-id="067dc-602">Ustaw metodę HTTP, aby **uzyskać**.</span><span class="sxs-lookup"><span data-stu-id="067dc-602">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="067dc-603">Ustaw identyfikator URI żądania na `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="067dc-603">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="067dc-604">Na przykład `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="067dc-604">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="067dc-605">Ustaw **dwa widoki okienka** w programie Poster.</span><span class="sxs-lookup"><span data-stu-id="067dc-605">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="067dc-606">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="067dc-606">Select **Send**.</span></span>

<span data-ttu-id="067dc-607">Ta aplikacja używa bazy danych w pamięci.</span><span class="sxs-lookup"><span data-stu-id="067dc-607">This app uses an in-memory database.</span></span> <span data-ttu-id="067dc-608">Jeśli aplikacja zostanie zatrzymana i uruchomiona, poprzednie żądanie GET nie zwróci żadnych danych.</span><span class="sxs-lookup"><span data-stu-id="067dc-608">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="067dc-609">Jeśli nie zostaną zwrócone żadne dane, [Opublikuj](#post) dane w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="067dc-609">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="067dc-610">Ścieżki routingu i adresów URL</span><span class="sxs-lookup"><span data-stu-id="067dc-610">Routing and URL paths</span></span>

<span data-ttu-id="067dc-611">Ten [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) atrybut oznacza metodę, która reaguje na żądanie HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="067dc-611">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="067dc-612">Ścieżka adresu URL dla każdej metody jest zbudowana w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="067dc-612">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="067dc-613">Zacznij od ciągu szablonu w `Route` atrybucie kontrolera:</span><span class="sxs-lookup"><span data-stu-id="067dc-613">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="067dc-614">Zastąp `[controller]` nazwą kontrolera, którą Konwencją jest nazwa klasy kontrolera minus sufiks "Controller".</span><span class="sxs-lookup"><span data-stu-id="067dc-614">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="067dc-615">Dla tego przykładu nazwa klasy kontrolera to **TodoItems** Controller, więc nazwa kontrolera to "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="067dc-615">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="067dc-616">W ASP.NET Core [routingu](xref:mvc/controllers/routing) jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="067dc-616">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="067dc-617">Jeśli `[HttpGet]` atrybut ma szablon trasy (na przykład `[HttpGet("products")]` ), Dodaj go do ścieżki.</span><span class="sxs-lookup"><span data-stu-id="067dc-617">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="067dc-618">Ten przykład nie używa szablonu.</span><span class="sxs-lookup"><span data-stu-id="067dc-618">This sample doesn't use a template.</span></span> <span data-ttu-id="067dc-619">Aby uzyskać więcej informacji, zobacz temat [Routing atrybutów z atrybutami http [Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="067dc-619">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="067dc-620">W poniższej `GetTodoItem` metodzie `"{id}"` jest zmienną zastępczą dla unikatowego identyfikatora elementu do wykonania.</span><span class="sxs-lookup"><span data-stu-id="067dc-620">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="067dc-621">Gdy `GetTodoItem` jest wywoływana, wartość `"{id}"` w adresie URL jest podawana do metody w jej `id` parametrze.</span><span class="sxs-lookup"><span data-stu-id="067dc-621">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="067dc-622">Wartości zwracane</span><span class="sxs-lookup"><span data-stu-id="067dc-622">Return values</span></span> 

<span data-ttu-id="067dc-623">Zwracany typ `GetTodoItems` `GetTodoItem` metod i jest [ \<T> typem ActionResult](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="067dc-623">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="067dc-624">ASP.NET Core automatycznie serializować obiektu do [formatu JSON](https://www.json.org/) i zapisuje kod JSON w treści komunikatu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="067dc-624">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="067dc-625">Kod odpowiedzi dla tego typu zwracanego to 200, przy założeniu, że nie istnieją Nieobsłużone wyjątki.</span><span class="sxs-lookup"><span data-stu-id="067dc-625">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="067dc-626">Nieobsłużone wyjątki są tłumaczone na błędy 5xx.</span><span class="sxs-lookup"><span data-stu-id="067dc-626">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="067dc-627">`ActionResult` typy zwracane mogą reprezentować szeroką gamę kodów stanu HTTP.</span><span class="sxs-lookup"><span data-stu-id="067dc-627">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="067dc-628">Na przykład `GetTodoItem` może zwracać dwie różne wartości stanu:</span><span class="sxs-lookup"><span data-stu-id="067dc-628">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="067dc-629">Jeśli żaden element nie jest zgodny z żądanym IDENTYFIKATORem, metoda zwróci <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> Kod błędu 404.</span><span class="sxs-lookup"><span data-stu-id="067dc-629">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="067dc-630">W przeciwnym razie metoda zwraca 200 z treścią odpowiedzi JSON.</span><span class="sxs-lookup"><span data-stu-id="067dc-630">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="067dc-631">Zwracanie `item` wyników w odpowiedzi HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="067dc-631">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="067dc-632">Metoda PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="067dc-632">The PutTodoItem method</span></span>

<span data-ttu-id="067dc-633">Przeanalizuj metodę `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="067dc-633">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="067dc-634">`PutTodoItem` jest podobny do `PostTodoItem` , z tą różnicą, że używa protokołu HTTP Put.</span><span class="sxs-lookup"><span data-stu-id="067dc-634">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="067dc-635">Odpowiedź to [204 (brak zawartości)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="067dc-635">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="067dc-636">Zgodnie ze specyfikacją protokołu HTTP żądanie PUT wymaga, aby klient wysłał całą zaktualizowaną jednostkę, a nie tylko te zmiany.</span><span class="sxs-lookup"><span data-stu-id="067dc-636">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="067dc-637">Aby zapewnić obsługę częściowych aktualizacji, użyj [poprawki http](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="067dc-637">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="067dc-638">Jeśli wystąpi błąd podczas wywoływania `PutTodoItem` , wywołaj, `GET` Aby upewnić się, że w bazie danych znajduje się element.</span><span class="sxs-lookup"><span data-stu-id="067dc-638">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="067dc-639">Testowanie metody PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="067dc-639">Test the PutTodoItem method</span></span>

<span data-ttu-id="067dc-640">Ten przykład korzysta z bazy danych w pamięci, która musi zostać zainicjowana za każdym razem, gdy aplikacja zostanie uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="067dc-640">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="067dc-641">Przed wykonaniem wywołania PUT musi istnieć element w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="067dc-641">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="067dc-642">Przed wykonaniem wywołania PUT należy wywołać metodę GET, aby upewnić się, że istnieje element w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="067dc-642">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="067dc-643">Zaktualizuj element do wykonania o identyfikatorze 1 i ustaw jego nazwę na "Źródło danych":</span><span class="sxs-lookup"><span data-stu-id="067dc-643">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="067dc-644">Na poniższej ilustracji przedstawiono aktualizację programu Poster:</span><span class="sxs-lookup"><span data-stu-id="067dc-644">The following image shows the Postman update:</span></span>

![Konsola programu Poster pokazująca odpowiedź 204 (brak zawartości)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="067dc-646">Metoda DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="067dc-646">The DeleteTodoItem method</span></span>

<span data-ttu-id="067dc-647">Przeanalizuj metodę `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="067dc-647">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="067dc-648">Testowanie metody DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="067dc-648">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="067dc-649">Użyj programu Poster, aby usunąć element do wykonania:</span><span class="sxs-lookup"><span data-stu-id="067dc-649">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="067dc-650">Ustaw metodę na `DELETE` .</span><span class="sxs-lookup"><span data-stu-id="067dc-650">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="067dc-651">Ustaw identyfikator URI obiektu do usunięcia (na przykład `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="067dc-651">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="067dc-652">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="067dc-652">Select **Send**.</span></span>

<a name="over-post"></a>
<a name="over-post-v3"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="067dc-653">Zapobiegaj za pośrednictwem księgowania</span><span class="sxs-lookup"><span data-stu-id="067dc-653">Prevent over-posting</span></span>

<span data-ttu-id="067dc-654">Obecnie Przykładowa aplikacja uwidacznia cały `TodoItem` obiekt.</span><span class="sxs-lookup"><span data-stu-id="067dc-654">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="067dc-655">Aplikacje produkcyjne zwykle ograniczają dane wejściowe i zwracane przy użyciu podzestawu modelu.</span><span class="sxs-lookup"><span data-stu-id="067dc-655">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="067dc-656">Istnieje wiele powodów związanych z tym, a zabezpieczenia są głównymi.</span><span class="sxs-lookup"><span data-stu-id="067dc-656">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="067dc-657">Podzestaw modelu jest zwykle określany jako obiekt Transfer danych (DTO), model wejściowy lub model widoku.</span><span class="sxs-lookup"><span data-stu-id="067dc-657">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="067dc-658">**DTO** jest używany w tym artykule.</span><span class="sxs-lookup"><span data-stu-id="067dc-658">**DTO** is used in this article.</span></span>

<span data-ttu-id="067dc-659">DTO może służyć do:</span><span class="sxs-lookup"><span data-stu-id="067dc-659">A DTO may be used to:</span></span>

* <span data-ttu-id="067dc-660">Zablokuj nadmierne księgowanie.</span><span class="sxs-lookup"><span data-stu-id="067dc-660">Prevent over-posting.</span></span>
* <span data-ttu-id="067dc-661">Ukryj właściwości, które nie powinny być wyświetlane dla klientów.</span><span class="sxs-lookup"><span data-stu-id="067dc-661">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="067dc-662">Pomiń niektóre właściwości, aby zmniejszyć rozmiar ładunku.</span><span class="sxs-lookup"><span data-stu-id="067dc-662">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="067dc-663">Spłaszcz wykresy obiektów zawierające obiekty zagnieżdżone.</span><span class="sxs-lookup"><span data-stu-id="067dc-663">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="067dc-664">Spłaszczone wykresy obiektów mogą być wygodniejsze dla klientów.</span><span class="sxs-lookup"><span data-stu-id="067dc-664">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="067dc-665">Aby zademonstrować podejście DTO, zaktualizuj `TodoItem` klasę w celu uwzględnienia pola tajnego:</span><span class="sxs-lookup"><span data-stu-id="067dc-665">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="067dc-666">Pole tajne musi być ukryte w tej aplikacji, ale aplikacja administracyjna mogła ją uwidocznić.</span><span class="sxs-lookup"><span data-stu-id="067dc-666">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="067dc-667">Sprawdź, czy można opublikować i pobrać pole tajne.</span><span class="sxs-lookup"><span data-stu-id="067dc-667">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="067dc-668">Utwórz model DTO:</span><span class="sxs-lookup"><span data-stu-id="067dc-668">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="067dc-669">Zaktualizuj, `TodoItemsController` Aby użyć `TodoItemDTO` :</span><span class="sxs-lookup"><span data-stu-id="067dc-669">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="067dc-670">Upewnij się, że nie można opublikować lub pobrać pola tajnego.</span><span class="sxs-lookup"><span data-stu-id="067dc-670">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="067dc-671">Wywoływanie interfejsu API sieci Web przy użyciu języka JavaScript</span><span class="sxs-lookup"><span data-stu-id="067dc-671">Call the web API with JavaScript</span></span>

<span data-ttu-id="067dc-672">Zobacz [Samouczek: wywoływanie interfejsu API sieci web ASP.NET Core przy użyciu języka JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="067dc-672">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="067dc-673">Ten samouczek zawiera informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="067dc-673">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="067dc-674">Utwórz projekt interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="067dc-674">Create a web API project.</span></span>
> * <span data-ttu-id="067dc-675">Dodaj klasę modelu i kontekst bazy danych.</span><span class="sxs-lookup"><span data-stu-id="067dc-675">Add a model class and a database context.</span></span>
> * <span data-ttu-id="067dc-676">Dodaj kontroler.</span><span class="sxs-lookup"><span data-stu-id="067dc-676">Add a controller.</span></span>
> * <span data-ttu-id="067dc-677">Dodaj metody CRUD.</span><span class="sxs-lookup"><span data-stu-id="067dc-677">Add CRUD methods.</span></span>
> * <span data-ttu-id="067dc-678">Skonfiguruj ścieżki routingu i adresów URL.</span><span class="sxs-lookup"><span data-stu-id="067dc-678">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="067dc-679">Określ wartości zwracane.</span><span class="sxs-lookup"><span data-stu-id="067dc-679">Specify return values.</span></span>
> * <span data-ttu-id="067dc-680">Wywołaj interfejs API sieci Web za pomocą programu Poster.</span><span class="sxs-lookup"><span data-stu-id="067dc-680">Call the web API with Postman.</span></span>
> * <span data-ttu-id="067dc-681">Wywołaj interfejs API sieci Web za pomocą języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="067dc-681">Call the web API with JavaScript.</span></span>

<span data-ttu-id="067dc-682">Na końcu znajduje się internetowy interfejs API, który może zarządzać elementami do wykonania przechowywanymi w relacyjnej bazie danych.</span><span class="sxs-lookup"><span data-stu-id="067dc-682">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview-21"></a><span data-ttu-id="067dc-683">Przegląd 2,1</span><span class="sxs-lookup"><span data-stu-id="067dc-683">Overview 2.1</span></span>

<span data-ttu-id="067dc-684">Ten samouczek tworzy następujący interfejs API:</span><span class="sxs-lookup"><span data-stu-id="067dc-684">This tutorial creates the following API:</span></span>

|<span data-ttu-id="067dc-685">Interfejs API</span><span class="sxs-lookup"><span data-stu-id="067dc-685">API</span></span> | <span data-ttu-id="067dc-686">Opis</span><span class="sxs-lookup"><span data-stu-id="067dc-686">Description</span></span> | <span data-ttu-id="067dc-687">Treść żądania</span><span class="sxs-lookup"><span data-stu-id="067dc-687">Request body</span></span> | <span data-ttu-id="067dc-688">Treść odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="067dc-688">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="067dc-689">Pobierz/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="067dc-689">GET /api/TodoItems</span></span> | <span data-ttu-id="067dc-690">Pobierz wszystkie elementy do wykonania</span><span class="sxs-lookup"><span data-stu-id="067dc-690">Get all to-do items</span></span> | <span data-ttu-id="067dc-691">Brak</span><span class="sxs-lookup"><span data-stu-id="067dc-691">None</span></span> | <span data-ttu-id="067dc-692">Tablica elementów do wykonania</span><span class="sxs-lookup"><span data-stu-id="067dc-692">Array of to-do items</span></span>|
|<span data-ttu-id="067dc-693">Pobierz/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="067dc-693">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="067dc-694">Pobieranie elementu według identyfikatora</span><span class="sxs-lookup"><span data-stu-id="067dc-694">Get an item by ID</span></span> | <span data-ttu-id="067dc-695">Brak</span><span class="sxs-lookup"><span data-stu-id="067dc-695">None</span></span> | <span data-ttu-id="067dc-696">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="067dc-696">To-do item</span></span>|
|<span data-ttu-id="067dc-697">Opublikuj/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="067dc-697">POST /api/TodoItems</span></span> | <span data-ttu-id="067dc-698">Dodaj nowy element</span><span class="sxs-lookup"><span data-stu-id="067dc-698">Add a new item</span></span> | <span data-ttu-id="067dc-699">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="067dc-699">To-do item</span></span> | <span data-ttu-id="067dc-700">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="067dc-700">To-do item</span></span> |
|<span data-ttu-id="067dc-701">Umieść/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="067dc-701">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="067dc-702">Aktualizowanie istniejącego elementu &nbsp;</span><span class="sxs-lookup"><span data-stu-id="067dc-702">Update an existing item &nbsp;</span></span> | <span data-ttu-id="067dc-703">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="067dc-703">To-do item</span></span> | <span data-ttu-id="067dc-704">Brak</span><span class="sxs-lookup"><span data-stu-id="067dc-704">None</span></span> |
|<span data-ttu-id="067dc-705">Usuń/api/TodoItems/{id} &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="067dc-705">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="067dc-706">Usuń element &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="067dc-706">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="067dc-707">Brak</span><span class="sxs-lookup"><span data-stu-id="067dc-707">None</span></span> | <span data-ttu-id="067dc-708">Brak</span><span class="sxs-lookup"><span data-stu-id="067dc-708">None</span></span>|

<span data-ttu-id="067dc-709">Na poniższym diagramie przedstawiono projekt aplikacji.</span><span class="sxs-lookup"><span data-stu-id="067dc-709">The following diagram shows the design of the app.</span></span>

![Klient jest reprezentowany przez pole po lewej stronie.](first-web-api/_static/architecture.png)

## <a name="prerequisites-21"></a><span data-ttu-id="067dc-715">Wymagania wstępne 2,1</span><span class="sxs-lookup"><span data-stu-id="067dc-715">Prerequisites 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="067dc-716">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="067dc-716">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="067dc-717">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="067dc-717">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="067dc-718">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="067dc-718">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project-21"></a><span data-ttu-id="067dc-719">Tworzenie projektu sieci Web 2,1</span><span class="sxs-lookup"><span data-stu-id="067dc-719">Create a web project 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="067dc-720">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="067dc-720">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="067dc-721">Z menu **plik** wybierz pozycję **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="067dc-721">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="067dc-722">Wybierz szablon **aplikacja sieci Web ASP.NET Core** a następnie kliknij przycisk **dalej**.</span><span class="sxs-lookup"><span data-stu-id="067dc-722">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="067dc-723">Nazwij projekt *TodoApi* i kliknij pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="067dc-723">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="067dc-724">W oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** upewnij się, że wybrano opcję **.net Core** i **ASP.NET Core 2,2** .</span><span class="sxs-lookup"><span data-stu-id="067dc-724">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="067dc-725">Wybierz szablon **interfejsu API** i kliknij przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="067dc-725">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="067dc-726">**Nie** zaznaczaj opcji **Włącz obsługę platformy Docker**.</span><span class="sxs-lookup"><span data-stu-id="067dc-726">**Don't** select **Enable Docker Support**.</span></span>

![Okno dialogowe programu VS New Project](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="067dc-728">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="067dc-728">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="067dc-729">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="067dc-729">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="067dc-730">Zmień katalog ( `cd` ) do folderu, który będzie zawierać folder projektu.</span><span class="sxs-lookup"><span data-stu-id="067dc-730">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="067dc-731">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="067dc-731">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="067dc-732">Te polecenia tworzą nowy projekt internetowego interfejsu API i otwierają nowe wystąpienie Visual Studio Code w nowym folderze projektu.</span><span class="sxs-lookup"><span data-stu-id="067dc-732">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="067dc-733">Gdy zostanie wyświetlone okno dialogowe z pytaniem, czy chcesz dodać wymagane zasoby do projektu, wybierz opcję **tak**.</span><span class="sxs-lookup"><span data-stu-id="067dc-733">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="067dc-734">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="067dc-734">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="067dc-735">Wybierz pozycję **plik** > **nowe rozwiązanie**.</span><span class="sxs-lookup"><span data-stu-id="067dc-735">Select **File** > **New Solution**.</span></span>

  ![macOS nowe rozwiązanie](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="067dc-737">W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz pozycję  >    >  **interfejs API** aplikacji .NET Core  >  .</span><span class="sxs-lookup"><span data-stu-id="067dc-737">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="067dc-738">W wersji 8,6 lub nowszej wybierz pozycję   >    >  **interfejs API**  >  aplikacji sieci Web i konsoli.</span><span class="sxs-lookup"><span data-stu-id="067dc-738">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="067dc-739">W oknie dialogowym **Konfigurowanie nowego interfejsu API sieci Web ASP.NET Core** wybierz najnowszą platformę **docelową** programu .NET Core 2. x.</span><span class="sxs-lookup"><span data-stu-id="067dc-739">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="067dc-740">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="067dc-740">Select **Next**.</span></span>

* <span data-ttu-id="067dc-741">Wprowadź *TodoApi* jako **nazwę projektu** , a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="067dc-741">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![okno dialogowe konfiguracji](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api-21"></a><span data-ttu-id="067dc-743">Testowanie interfejsu API 2,1</span><span class="sxs-lookup"><span data-stu-id="067dc-743">Test the API 2.1</span></span>

<span data-ttu-id="067dc-744">Szablon projektu tworzy `values` interfejs API.</span><span class="sxs-lookup"><span data-stu-id="067dc-744">The project template creates a `values` API.</span></span> <span data-ttu-id="067dc-745">Wywołaj `Get` metodę z przeglądarki, aby przetestować aplikację.</span><span class="sxs-lookup"><span data-stu-id="067dc-745">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="067dc-746">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="067dc-746">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="067dc-747">Naciśnij klawisze CTRL + F5, aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="067dc-747">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="067dc-748">Program Visual Studio uruchamia przeglądarkę i przechodzi do `https://localhost:<port>/api/values` lokalizacji, gdzie `<port>` jest losowo wybierany numer portu.</span><span class="sxs-lookup"><span data-stu-id="067dc-748">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="067dc-749">Jeśli zostanie wyświetlone okno dialogowe z pytaniem, czy należy zaufać certyfikatowi IIS Express, wybierz pozycję **tak**.</span><span class="sxs-lookup"><span data-stu-id="067dc-749">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="067dc-750">W wyświetlonym oknie dialogowym **ostrzeżenia o zabezpieczeniach** wybierz pozycję **tak**.</span><span class="sxs-lookup"><span data-stu-id="067dc-750">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="067dc-751">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="067dc-751">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="067dc-752">Naciśnij klawisze CTRL + F5, aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="067dc-752">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="067dc-753">W przeglądarce przejdź do następującego adresu URL: `https://localhost:5001/api/values` .</span><span class="sxs-lookup"><span data-stu-id="067dc-753">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="067dc-754">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="067dc-754">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="067dc-755">Wybierz pozycję **Uruchom**  >  **Rozpocznij debugowanie** , aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="067dc-755">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="067dc-756">Visual Studio dla komputerów Mac uruchamia przeglądarkę i nawiguje do `https://localhost:<port>` , gdzie `<port>` jest losowo wybranym numerem portu.</span><span class="sxs-lookup"><span data-stu-id="067dc-756">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="067dc-757">Zwracany jest błąd HTTP 404 (nie znaleziono).</span><span class="sxs-lookup"><span data-stu-id="067dc-757">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="067dc-758">Dołącz `/api/values` do adresu URL (Zmień adres URL na `https://localhost:<port>/api/values` ).</span><span class="sxs-lookup"><span data-stu-id="067dc-758">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="067dc-759">Zostanie zwrócony następujący kod JSON:</span><span class="sxs-lookup"><span data-stu-id="067dc-759">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class-21"></a><span data-ttu-id="067dc-760">Dodaj klasę modelu 2,1</span><span class="sxs-lookup"><span data-stu-id="067dc-760">Add a model class 2.1</span></span>

<span data-ttu-id="067dc-761">*Model* to zestaw klas, które reprezentują dane zarządzane przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="067dc-761">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="067dc-762">Model tej aplikacji jest pojedynczą `TodoItem` klasą.</span><span class="sxs-lookup"><span data-stu-id="067dc-762">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="067dc-763">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="067dc-763">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="067dc-764">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt.</span><span class="sxs-lookup"><span data-stu-id="067dc-764">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="067dc-765">Wybierz pozycję **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="067dc-765">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="067dc-766">Nazwij folder *Models* .</span><span class="sxs-lookup"><span data-stu-id="067dc-766">Name the folder *Models*.</span></span>

* <span data-ttu-id="067dc-767">Kliknij prawym przyciskiem myszy *Models* folder i wybierz polecenie **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="067dc-767">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="067dc-768">Nadaj klasie nazwę *TodoItem* i wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="067dc-768">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="067dc-769">Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="067dc-769">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="067dc-770">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="067dc-770">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="067dc-771">Dodaj folder o nazwie *Models* .</span><span class="sxs-lookup"><span data-stu-id="067dc-771">Add a folder named *Models*.</span></span>

* <span data-ttu-id="067dc-772">Dodaj `TodoItem` do *Models* folderu klasę o następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="067dc-772">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="067dc-773">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="067dc-773">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="067dc-774">Kliknij prawym przyciskiem myszy projekt.</span><span class="sxs-lookup"><span data-stu-id="067dc-774">Right-click the project.</span></span> <span data-ttu-id="067dc-775">Wybierz pozycję **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="067dc-775">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="067dc-776">Nazwij folder *Models* .</span><span class="sxs-lookup"><span data-stu-id="067dc-776">Name the folder *Models*.</span></span>

  ![Nowy folder](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="067dc-778">Kliknij prawym przyciskiem myszy *Models* folder, a następnie wybierz pozycję **Dodaj** > **nowy plik** > **ogólna** > **pusta Klasa**.</span><span class="sxs-lookup"><span data-stu-id="067dc-778">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="067dc-779">Nazwij klasę *TodoItem*, a następnie kliknij pozycję **New (nowy**).</span><span class="sxs-lookup"><span data-stu-id="067dc-779">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="067dc-780">Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="067dc-780">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="067dc-781">`Id`Właściwość działa jako unikatowy klucz w relacyjnej bazie danych.</span><span class="sxs-lookup"><span data-stu-id="067dc-781">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="067dc-782">Klasy modelu mogą przejść do dowolnego miejsca w projekcie, ale *Models* folder jest używany przez Konwencję.</span><span class="sxs-lookup"><span data-stu-id="067dc-782">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context-21"></a><span data-ttu-id="067dc-783">Dodawanie kontekstu bazy danych 2,1</span><span class="sxs-lookup"><span data-stu-id="067dc-783">Add a database context 2.1</span></span>

<span data-ttu-id="067dc-784">*Kontekst bazy danych* jest główną klasą, która koordynuje Entity Framework funkcji dla modelu danych.</span><span class="sxs-lookup"><span data-stu-id="067dc-784">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="067dc-785">Ta klasa jest tworzona przez wyprowadzanie z `Microsoft.EntityFrameworkCore.DbContext` klasy.</span><span class="sxs-lookup"><span data-stu-id="067dc-785">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="067dc-786">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="067dc-786">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="067dc-787">Kliknij prawym przyciskiem myszy *Models* folder i wybierz polecenie **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="067dc-787">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="067dc-788">Nadaj klasie nazwę *TodoContext* i kliknij przycisk **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="067dc-788">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="067dc-789">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="067dc-789">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="067dc-790">Dodaj `TodoContext` klasę do *Models* folderu.</span><span class="sxs-lookup"><span data-stu-id="067dc-790">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="067dc-791">Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="067dc-791">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context-21"></a><span data-ttu-id="067dc-792">Rejestrowanie kontekstu bazy danych 2,1</span><span class="sxs-lookup"><span data-stu-id="067dc-792">Register the database context 2.1</span></span>

<span data-ttu-id="067dc-793">W ASP.NET Core usługi, takie jak kontekst bazy danych, muszą być zarejestrowane z kontenerem [iniekcji zależności (di)](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="067dc-793">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="067dc-794">Kontener udostępnia usługę kontrolerom.</span><span class="sxs-lookup"><span data-stu-id="067dc-794">The container provides the service to controllers.</span></span>

<span data-ttu-id="067dc-795">Zaktualizuj *Startup.cs* o następujący wyróżniony kod:</span><span class="sxs-lookup"><span data-stu-id="067dc-795">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="067dc-796">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="067dc-796">The preceding code:</span></span>

* <span data-ttu-id="067dc-797">Usuwa nieużywane `using` deklaracje.</span><span class="sxs-lookup"><span data-stu-id="067dc-797">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="067dc-798">Dodaje kontekst bazy danych do kontenera DI.</span><span class="sxs-lookup"><span data-stu-id="067dc-798">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="067dc-799">Określa, że kontekst bazy danych będzie używać bazy danych w pamięci.</span><span class="sxs-lookup"><span data-stu-id="067dc-799">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller-21"></a><span data-ttu-id="067dc-800">Dodawanie kontrolera 2,1</span><span class="sxs-lookup"><span data-stu-id="067dc-800">Add a controller 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="067dc-801">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="067dc-801">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="067dc-802">Kliknij prawym przyciskiem myszy folder *controllers* .</span><span class="sxs-lookup"><span data-stu-id="067dc-802">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="067dc-803">Wybierz pozycję **Dodaj** > **nowy element**.</span><span class="sxs-lookup"><span data-stu-id="067dc-803">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="067dc-804">W oknie dialogowym **Dodaj nowy element** wybierz szablon **Klasa kontrolera interfejsu API** .</span><span class="sxs-lookup"><span data-stu-id="067dc-804">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="067dc-805">Nadaj klasie nazwę *TodoController* i wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="067dc-805">Name the class *TodoController*, and select **Add**.</span></span>

  ![Okno dialogowe Dodawanie nowego elementu z kontrolerem w polu wyszukiwania i wybranym kontrolerem interfejsu API sieci Web](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="067dc-807">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="067dc-807">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="067dc-808">W folderze *controllers* Utwórz klasę o nazwie `TodoController` .</span><span class="sxs-lookup"><span data-stu-id="067dc-808">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="067dc-809">Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="067dc-809">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="067dc-810">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="067dc-810">The preceding code:</span></span>

* <span data-ttu-id="067dc-811">Definiuje klasę kontrolera interfejsu API bez metod.</span><span class="sxs-lookup"><span data-stu-id="067dc-811">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="067dc-812">Oznacza klasę [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atrybutem.</span><span class="sxs-lookup"><span data-stu-id="067dc-812">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="067dc-813">Ten atrybut wskazuje, że kontroler odpowiada na żądania interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="067dc-813">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="067dc-814">Aby uzyskać informacje o określonych zachowaniach, które włącza atrybut, zobacz <xref:web-api/index> .</span><span class="sxs-lookup"><span data-stu-id="067dc-814">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="067dc-815">Używa funkcji DI do iniekcji kontekstu bazy danych ( `TodoContext` ) do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="067dc-815">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="067dc-816">Kontekst bazy danych jest używany w każdej z metod [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) w kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="067dc-816">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="067dc-817">Dodaje element o nazwie `Item1` do bazy danych, jeśli baza danych jest pusta.</span><span class="sxs-lookup"><span data-stu-id="067dc-817">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="067dc-818">Ten kod znajduje się w konstruktorze, więc jest uruchamiany za każdym razem, gdy istnieje nowe żądanie HTTP.</span><span class="sxs-lookup"><span data-stu-id="067dc-818">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="067dc-819">Jeśli usuniesz wszystkie elementy, Konstruktor zostanie ponownie utworzony `Item1` przy następnym wywołaniu metody interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="067dc-819">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="067dc-820">Dlatego może wyglądać podobnie jak usunięcie nie zadziałało, gdy rzeczywiście zadziałało.</span><span class="sxs-lookup"><span data-stu-id="067dc-820">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods-21"></a><span data-ttu-id="067dc-821">Dodaj metody get 2,1</span><span class="sxs-lookup"><span data-stu-id="067dc-821">Add Get methods 2.1</span></span>

<span data-ttu-id="067dc-822">Aby udostępnić interfejs API, który pobiera elementy do wykonania, Dodaj następujące metody do `TodoController` klasy:</span><span class="sxs-lookup"><span data-stu-id="067dc-822">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="067dc-823">Te metody implementują dwa punkty końcowe GET:</span><span class="sxs-lookup"><span data-stu-id="067dc-823">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="067dc-824">Zatrzymaj aplikację, jeśli jest nadal uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="067dc-824">Stop the app if it's still running.</span></span> <span data-ttu-id="067dc-825">Następnie uruchom ją ponownie, aby uwzględnić najnowsze zmiany.</span><span class="sxs-lookup"><span data-stu-id="067dc-825">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="067dc-826">Przetestuj aplikację, wywołując dwa punkty końcowe z przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="067dc-826">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="067dc-827">Przykład:</span><span class="sxs-lookup"><span data-stu-id="067dc-827">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="067dc-828">Następująca odpowiedź HTTP jest generowana przez wywołanie `GetTodoItems` :</span><span class="sxs-lookup"><span data-stu-id="067dc-828">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths-21"></a><span data-ttu-id="067dc-829">Ścieżki routingu i adresu URL 2,1</span><span class="sxs-lookup"><span data-stu-id="067dc-829">Routing and URL paths 2.1</span></span>

<span data-ttu-id="067dc-830">Ten [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) atrybut oznacza metodę, która reaguje na żądanie HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="067dc-830">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="067dc-831">Ścieżka adresu URL dla każdej metody jest zbudowana w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="067dc-831">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="067dc-832">Zacznij od ciągu szablonu w `Route` atrybucie kontrolera:</span><span class="sxs-lookup"><span data-stu-id="067dc-832">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="067dc-833">Zastąp `[controller]` nazwą kontrolera, którą Konwencją jest nazwa klasy kontrolera minus sufiks "Controller".</span><span class="sxs-lookup"><span data-stu-id="067dc-833">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="067dc-834">W przypadku tego przykładu nazwa klasy kontrolera to kontroler do **zrobienia**, więc nazwa kontrolera to "do zrobienia".</span><span class="sxs-lookup"><span data-stu-id="067dc-834">For this sample, the controller class name is **Todo** Controller, so the controller name is "todo".</span></span> <span data-ttu-id="067dc-835">W ASP.NET Core [routingu](xref:mvc/controllers/routing) jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="067dc-835">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="067dc-836">Jeśli `[HttpGet]` atrybut ma szablon trasy (na przykład `[HttpGet("products")]` ), Dodaj go do ścieżki.</span><span class="sxs-lookup"><span data-stu-id="067dc-836">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="067dc-837">Ten przykład nie używa szablonu.</span><span class="sxs-lookup"><span data-stu-id="067dc-837">This sample doesn't use a template.</span></span> <span data-ttu-id="067dc-838">Aby uzyskać więcej informacji, zobacz temat [Routing atrybutów z atrybutami http [Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="067dc-838">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="067dc-839">W poniższej `GetTodoItem` metodzie `"{id}"` jest zmienną zastępczą dla unikatowego identyfikatora elementu do wykonania.</span><span class="sxs-lookup"><span data-stu-id="067dc-839">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="067dc-840">Gdy `GetTodoItem` jest wywoływana, wartość `"{id}"` w adresie URL jest podawana do metody w jej `id` parametrze.</span><span class="sxs-lookup"><span data-stu-id="067dc-840">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values-21"></a><span data-ttu-id="067dc-841">Wartości zwracane 2,1</span><span class="sxs-lookup"><span data-stu-id="067dc-841">Return values 2.1</span></span>

<span data-ttu-id="067dc-842">Zwracany typ `GetTodoItems` `GetTodoItem` metod i jest [ \<T> typem ActionResult](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="067dc-842">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="067dc-843">ASP.NET Core automatycznie serializować obiektu do [formatu JSON](https://www.json.org/) i zapisuje kod JSON w treści komunikatu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="067dc-843">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="067dc-844">Kod odpowiedzi dla tego typu zwracanego to 200, przy założeniu, że nie istnieją Nieobsłużone wyjątki.</span><span class="sxs-lookup"><span data-stu-id="067dc-844">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="067dc-845">Nieobsłużone wyjątki są tłumaczone na błędy 5xx.</span><span class="sxs-lookup"><span data-stu-id="067dc-845">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="067dc-846">`ActionResult` typy zwracane mogą reprezentować szeroką gamę kodów stanu HTTP.</span><span class="sxs-lookup"><span data-stu-id="067dc-846">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="067dc-847">Na przykład `GetTodoItem` może zwracać dwie różne wartości stanu:</span><span class="sxs-lookup"><span data-stu-id="067dc-847">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="067dc-848">Jeśli żaden element nie jest zgodny z żądanym IDENTYFIKATORem, metoda zwróci <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> Kod błędu 404.</span><span class="sxs-lookup"><span data-stu-id="067dc-848">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="067dc-849">W przeciwnym razie metoda zwraca 200 z treścią odpowiedzi JSON.</span><span class="sxs-lookup"><span data-stu-id="067dc-849">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="067dc-850">Zwracanie `item` wyników w odpowiedzi HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="067dc-850">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method-21"></a><span data-ttu-id="067dc-851">Testowanie metody GetTodoItems 2,1</span><span class="sxs-lookup"><span data-stu-id="067dc-851">Test the GetTodoItems method 2.1</span></span>

<span data-ttu-id="067dc-852">Ten samouczek używa programu do testowania interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="067dc-852">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="067dc-853">Zainstaluj program [Poster](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="067dc-853">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="067dc-854">Uruchom aplikację internetową.</span><span class="sxs-lookup"><span data-stu-id="067dc-854">Start the web app.</span></span>
* <span data-ttu-id="067dc-855">Uruchom wpis.</span><span class="sxs-lookup"><span data-stu-id="067dc-855">Start Postman.</span></span>
* <span data-ttu-id="067dc-856">Wyłącz **weryfikację certyfikatu SSL**.</span><span class="sxs-lookup"><span data-stu-id="067dc-856">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="067dc-857">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="067dc-857">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="067dc-858">W  obszarze > **Ustawienia** pliku (karta **Ogólne** ) Wyłącz **weryfikację certyfikatu SSL**.</span><span class="sxs-lookup"><span data-stu-id="067dc-858">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="067dc-859">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="067dc-859">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="067dc-860">Z poziomu preferencji programu **Poster**  >   (karta **Ogólne** ) Wyłącz **weryfikację certyfikatu SSL**.</span><span class="sxs-lookup"><span data-stu-id="067dc-860">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="067dc-861">Alternatywnie wybierz klucz i wybierz pozycję **Ustawienia**, a następnie wyłącz weryfikację certyfikatu SSL.</span><span class="sxs-lookup"><span data-stu-id="067dc-861">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="067dc-862">Po przetestowaniu kontrolera ponownie Włącz weryfikację certyfikatu SSL.</span><span class="sxs-lookup"><span data-stu-id="067dc-862">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="067dc-863">Utwórz nowe żądanie.</span><span class="sxs-lookup"><span data-stu-id="067dc-863">Create a new request.</span></span>
  * <span data-ttu-id="067dc-864">Ustaw metodę HTTP, aby **uzyskać**.</span><span class="sxs-lookup"><span data-stu-id="067dc-864">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="067dc-865">Ustaw identyfikator URI żądania na `https://localhost:<port>/api/todo` .</span><span class="sxs-lookup"><span data-stu-id="067dc-865">Set the request URI to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="067dc-866">Na przykład `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="067dc-866">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="067dc-867">Ustaw **dwa widoki okienka** w programie Poster.</span><span class="sxs-lookup"><span data-stu-id="067dc-867">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="067dc-868">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="067dc-868">Select **Send**.</span></span>

![Ogłoś przy użyciu żądania GET](first-web-api/_static/2pv.png)

## <a name="add-a-create-method-21"></a><span data-ttu-id="067dc-870">Dodawanie metody Create 2,1</span><span class="sxs-lookup"><span data-stu-id="067dc-870">Add a Create method 2.1</span></span>

<span data-ttu-id="067dc-871">Dodaj następującą `PostTodoItem` metodę w obszarze *controllers/TodoController. cs*:</span><span class="sxs-lookup"><span data-stu-id="067dc-871">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="067dc-872">Poprzedni kod jest metodą POST protokołu HTTP, jak wskazano w [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) atrybucie.</span><span class="sxs-lookup"><span data-stu-id="067dc-872">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="067dc-873">Metoda pobiera wartość elementu do wykonania z treści żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="067dc-873">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="067dc-874">`CreatedAtAction`Metoda:</span><span class="sxs-lookup"><span data-stu-id="067dc-874">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="067dc-875">Zwraca kod stanu HTTP 201, jeśli powodzenie.</span><span class="sxs-lookup"><span data-stu-id="067dc-875">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="067dc-876">HTTP 201 to standardowa odpowiedź dla metody POST protokołu HTTP, która tworzy nowy zasób na serwerze.</span><span class="sxs-lookup"><span data-stu-id="067dc-876">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="067dc-877">Dodaje `Location` nagłówek do odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="067dc-877">Adds a `Location` header to the response.</span></span> <span data-ttu-id="067dc-878">`Location`Nagłówek określa identyfikator URI nowo utworzonego elementu do wykonania.</span><span class="sxs-lookup"><span data-stu-id="067dc-878">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="067dc-879">Aby uzyskać więcej informacji, zobacz [10.2.2 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="067dc-879">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="067dc-880">Odwołuje się do `GetTodoItem` akcji tworzenia `Location` identyfikatora URI nagłówka.</span><span class="sxs-lookup"><span data-stu-id="067dc-880">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="067dc-881">`nameof`Słowo kluczowe języka C# służy do zapobiegania twardemu kodowaniu nazwy akcji w `CreatedAtAction` wywołaniu.</span><span class="sxs-lookup"><span data-stu-id="067dc-881">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method-21"></a><span data-ttu-id="067dc-882">Testowanie metody PostTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="067dc-882">Test the PostTodoItem method 2.1</span></span>

* <span data-ttu-id="067dc-883">Skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="067dc-883">Build the project.</span></span>
* <span data-ttu-id="067dc-884">W programie Poster ustaw metodę HTTP na `POST` .</span><span class="sxs-lookup"><span data-stu-id="067dc-884">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="067dc-885">Ustaw identyfikator URI na `https://localhost:<port>/api/Todo` .</span><span class="sxs-lookup"><span data-stu-id="067dc-885">Set the URI to `https://localhost:<port>/api/Todo`.</span></span> <span data-ttu-id="067dc-886">Na przykład `https://localhost:5001/api/Todo`.</span><span class="sxs-lookup"><span data-stu-id="067dc-886">For example, `https://localhost:5001/api/Todo`.</span></span>
* <span data-ttu-id="067dc-887">Wybierz kartę **Treść**.</span><span class="sxs-lookup"><span data-stu-id="067dc-887">Select the **Body** tab.</span></span>
* <span data-ttu-id="067dc-888">Wybierz przycisk radiowy **RAW** .</span><span class="sxs-lookup"><span data-stu-id="067dc-888">Select the **raw** radio button.</span></span>
* <span data-ttu-id="067dc-889">Ustaw typ na **JSON (Application/JSON)**.</span><span class="sxs-lookup"><span data-stu-id="067dc-889">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="067dc-890">W treści żądania wprowadź kod JSON dla elementu do wykonania:</span><span class="sxs-lookup"><span data-stu-id="067dc-890">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="067dc-891">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="067dc-891">Select **Send**.</span></span>

  ![Ogłoś przy użyciu żądania Create](first-web-api/_static/create.png)

  <span data-ttu-id="067dc-893">Jeśli wystąpi błąd 405 metody niedozwolonej, jest to prawdopodobnie wynik niekompilowania projektu po dodaniu `PostTodoItem` metody.</span><span class="sxs-lookup"><span data-stu-id="067dc-893">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri-21"></a><span data-ttu-id="067dc-894">Testowanie identyfikatora URI nagłówka lokalizacji 2,1</span><span class="sxs-lookup"><span data-stu-id="067dc-894">Test the location header URI 2.1</span></span>

* <span data-ttu-id="067dc-895">Wybierz kartę **nagłówki** w okienku **odpowiedź** .</span><span class="sxs-lookup"><span data-stu-id="067dc-895">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="067dc-896">Skopiuj wartość nagłówka **lokalizacji** :</span><span class="sxs-lookup"><span data-stu-id="067dc-896">Copy the **Location** header value:</span></span>

  ![Karta nagłówki w konsoli programu Poster](first-web-api/_static/pmc2.png)

* <span data-ttu-id="067dc-898">Ustaw metodę, aby uzyskać.</span><span class="sxs-lookup"><span data-stu-id="067dc-898">Set the method to GET.</span></span>
* <span data-ttu-id="067dc-899">Ustaw identyfikator URI na `https://localhost:<port>/api/TodoItems/2` .</span><span class="sxs-lookup"><span data-stu-id="067dc-899">Set the URI to `https://localhost:<port>/api/TodoItems/2`.</span></span> <span data-ttu-id="067dc-900">Na przykład `https://localhost:5001/api/TodoItems/2`.</span><span class="sxs-lookup"><span data-stu-id="067dc-900">For example, `https://localhost:5001/api/TodoItems/2`.</span></span>
* <span data-ttu-id="067dc-901">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="067dc-901">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method-21"></a><span data-ttu-id="067dc-902">Dodawanie metody PutTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="067dc-902">Add a PutTodoItem method 2.1</span></span>

<span data-ttu-id="067dc-903">Dodaj następującą `PutTodoItem` metodę:</span><span class="sxs-lookup"><span data-stu-id="067dc-903">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="067dc-904">`PutTodoItem` jest podobny do `PostTodoItem` , z tą różnicą, że używa protokołu HTTP Put.</span><span class="sxs-lookup"><span data-stu-id="067dc-904">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="067dc-905">Odpowiedź to [204 (brak zawartości)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="067dc-905">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="067dc-906">Zgodnie ze specyfikacją protokołu HTTP żądanie PUT wymaga, aby klient wysłał całą zaktualizowaną jednostkę, a nie tylko te zmiany.</span><span class="sxs-lookup"><span data-stu-id="067dc-906">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="067dc-907">Aby zapewnić obsługę częściowych aktualizacji, użyj [poprawki http](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="067dc-907">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="067dc-908">Jeśli wystąpi błąd podczas wywoływania `PutTodoItem` , wywołaj, `GET` Aby upewnić się, że w bazie danych znajduje się element.</span><span class="sxs-lookup"><span data-stu-id="067dc-908">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method-21"></a><span data-ttu-id="067dc-909">Testowanie metody PutTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="067dc-909">Test the PutTodoItem method 2.1</span></span>

<span data-ttu-id="067dc-910">Ten przykład korzysta z bazy danych w pamięci, która musi zostać zainicjowana za każdym razem, gdy aplikacja zostanie uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="067dc-910">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="067dc-911">Przed wykonaniem wywołania PUT musi istnieć element w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="067dc-911">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="067dc-912">Przed wykonaniem wywołania PUT należy wywołać metodę GET, aby upewnić się, że istnieje element w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="067dc-912">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="067dc-913">Zaktualizuj element do wykonania o identyfikatorze 1 i ustaw jego nazwę na "Źródło danych":</span><span class="sxs-lookup"><span data-stu-id="067dc-913">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="067dc-914">Na poniższej ilustracji przedstawiono aktualizację programu Poster:</span><span class="sxs-lookup"><span data-stu-id="067dc-914">The following image shows the Postman update:</span></span>

![Konsola programu Poster pokazująca odpowiedź 204 (brak zawartości)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method-21"></a><span data-ttu-id="067dc-916">Dodawanie metody DeleteTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="067dc-916">Add a DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="067dc-917">Dodaj następującą `DeleteTodoItem` metodę:</span><span class="sxs-lookup"><span data-stu-id="067dc-917">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="067dc-918">`DeleteTodoItem`Odpowiedź to [204 (brak zawartości)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="067dc-918">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method-21"></a><span data-ttu-id="067dc-919">Testowanie metody DeleteTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="067dc-919">Test the DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="067dc-920">Użyj programu Poster, aby usunąć element do wykonania:</span><span class="sxs-lookup"><span data-stu-id="067dc-920">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="067dc-921">Ustaw metodę na `DELETE` .</span><span class="sxs-lookup"><span data-stu-id="067dc-921">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="067dc-922">Ustaw identyfikator URI obiektu do usunięcia (na przykład `https://localhost:5001/api/todo/1` ).</span><span class="sxs-lookup"><span data-stu-id="067dc-922">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="067dc-923">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="067dc-923">Select **Send**.</span></span>

<span data-ttu-id="067dc-924">Przykładowa aplikacja umożliwia usunięcie wszystkich elementów.</span><span class="sxs-lookup"><span data-stu-id="067dc-924">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="067dc-925">Jednak po usunięciu ostatniego elementu jest on tworzony przez konstruktora klasy modelu przy następnym wywołaniu interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="067dc-925">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript-21"></a><span data-ttu-id="067dc-926">Wywoływanie interfejsu API sieci Web przy użyciu języka JavaScript 2,1</span><span class="sxs-lookup"><span data-stu-id="067dc-926">Call the web API with JavaScript 2.1</span></span>

<span data-ttu-id="067dc-927">W tej sekcji zostanie dodana strona HTML, która używa języka JavaScript do wywoływania internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="067dc-927">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="067dc-928">jQuery inicjuje żądanie.</span><span class="sxs-lookup"><span data-stu-id="067dc-928">jQuery initiates the request.</span></span> <span data-ttu-id="067dc-929">Język JavaScript aktualizuje stronę ze szczegółowymi informacjami z odpowiedzi internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="067dc-929">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="067dc-930">Skonfiguruj aplikację do [obsługi plików statycznych](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) i [Włącz domyślne mapowanie plików](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) , aktualizując *Startup.cs* z następującym wyróżnionym kodem:</span><span class="sxs-lookup"><span data-stu-id="067dc-930">Configure the app to [serve static files](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) and [enable default file mapping](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="067dc-931">Utwórz folder *wwwroot* w katalogu projektu.</span><span class="sxs-lookup"><span data-stu-id="067dc-931">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="067dc-932">Dodaj plik HTML o nazwie *index.html* do katalogu *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="067dc-932">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="067dc-933">Zastąp jego zawartość następującym znacznikiem:</span><span class="sxs-lookup"><span data-stu-id="067dc-933">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="067dc-934">Dodaj plik języka JavaScript o nazwie *site.js* do katalogu *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="067dc-934">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="067dc-935">Zastąp zawartość poniższym kodem:</span><span class="sxs-lookup"><span data-stu-id="067dc-935">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="067dc-936">Zmiana ustawień uruchamiania projektu ASP.NET Core może być wymagana do lokalnego przetestowania strony HTML:</span><span class="sxs-lookup"><span data-stu-id="067dc-936">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="067dc-937">Otwórz *Properties\launchSettings.jsna*.</span><span class="sxs-lookup"><span data-stu-id="067dc-937">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="067dc-938">Usuń `launchUrl` Właściwość, aby wymusić otwieranie przez aplikację w *index.html* &mdash; domyślnego pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="067dc-938">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="067dc-939">Ten przykład wywołuje wszystkie metody CRUD internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="067dc-939">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="067dc-940">Poniżej znajdują się wyjaśnienia wywołań interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="067dc-940">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items-21"></a><span data-ttu-id="067dc-941">Pobierz listę elementów do wykonania 2,1</span><span class="sxs-lookup"><span data-stu-id="067dc-941">Get a list of to-do items 2.1</span></span>

<span data-ttu-id="067dc-942">jQuery wysyła żądanie HTTP GET do internetowego interfejsu API, który zwraca kod JSON reprezentujący tablicę elementów do wykonania.</span><span class="sxs-lookup"><span data-stu-id="067dc-942">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="067dc-943">`success`Funkcja wywołania zwrotnego jest wywoływana, jeśli żądanie zakończy się pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="067dc-943">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="067dc-944">W wywołaniu zwrotnym model DOM jest aktualizowany przy użyciu informacji o tym do wykonania.</span><span class="sxs-lookup"><span data-stu-id="067dc-944">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item-21"></a><span data-ttu-id="067dc-945">Dodaj element do wykonania 2,1</span><span class="sxs-lookup"><span data-stu-id="067dc-945">Add a to-do item 2.1</span></span>

<span data-ttu-id="067dc-946">jQuery wysyła żądanie HTTP POST z elementem do wykonania w treści żądania.</span><span class="sxs-lookup"><span data-stu-id="067dc-946">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="067dc-947">`accepts`Opcje i `contentType` są ustawione na, aby `application/json` określić typ nośnika odbierany i wysyłany.</span><span class="sxs-lookup"><span data-stu-id="067dc-947">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="067dc-948">Element do wykonania jest konwertowany na format JSON przy użyciu [formatu JSON. stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="067dc-948">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="067dc-949">Gdy interfejs API zwraca kod stanu pomyślnego, `getData` Funkcja jest wywoływana w celu zaktualizowania tabeli HTML.</span><span class="sxs-lookup"><span data-stu-id="067dc-949">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item-21"></a><span data-ttu-id="067dc-950">Aktualizowanie elementu do wykonania 2,1</span><span class="sxs-lookup"><span data-stu-id="067dc-950">Update a to-do item 2.1</span></span>

<span data-ttu-id="067dc-951">Aktualizowanie elementu do wykonania jest podobne do dodawania jednego.</span><span class="sxs-lookup"><span data-stu-id="067dc-951">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="067dc-952">`url`Zmiany mające na celu dodanie unikatowego identyfikatora elementu i `type` is `PUT` .</span><span class="sxs-lookup"><span data-stu-id="067dc-952">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item-21"></a><span data-ttu-id="067dc-953">Usuń element do wykonania 2,1</span><span class="sxs-lookup"><span data-stu-id="067dc-953">Delete a to-do item 2.1</span></span>

<span data-ttu-id="067dc-954">Usuwanie elementu do wykonania jest realizowane przez ustawienie `type` w WYWOŁANIU AJAX `DELETE` i określenie unikatowego identyfikatora elementu w adresie URL.</span><span class="sxs-lookup"><span data-stu-id="067dc-954">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api-21"></a><span data-ttu-id="067dc-955">Dodawanie obsługi uwierzytelniania do internetowego interfejsu API 2,1</span><span class="sxs-lookup"><span data-stu-id="067dc-955">Add authentication support to a web API 2.1</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources-21"></a><span data-ttu-id="067dc-956">Dodatkowe zasoby 2,1</span><span class="sxs-lookup"><span data-stu-id="067dc-956">Additional resources 2.1</span></span>

<span data-ttu-id="067dc-957">[Wyświetl lub Pobierz przykładowy kod dla tego samouczka](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="067dc-957">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="067dc-958">Zobacz artykuł [jak pobrać](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="067dc-958">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="067dc-959">Więcej informacji można znaleźć w następujących zasobach:</span><span class="sxs-lookup"><span data-stu-id="067dc-959">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="067dc-960">Wersja tego samouczka usługi YouTube</span><span class="sxs-lookup"><span data-stu-id="067dc-960">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
